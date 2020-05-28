# __Good__
A piece of code I believe to be good is this example here.
 ```java
 /*
 * This handle function will take all request to the root or other urls.
 * Afterwards it will check for the different path in a switch
 */
 public void handle(HttpExchange exchange) throws IOException {
        db = new Users();
        BufferedReader br = new BufferedReader(new InputStreamReader(exchange.getRequestBody()));
        if (exchange.getRequestMethod().equalsIgnoreCase("POST")) {
            String line;
            while ((line = br.readLine()) != null) {
                JSONObject data = new JSONObject(line);
                String uri = exchange.getRequestURI().toString();

                switch (exchange.getRequestURI().toString()) {
                    case "/backend/vote":
                        handleVote(exchange, data);
                        break;
                    case "/backend/login":
                        handleLogin(exchange, data);
                        break;
                    case "/backend/register":
                        handleRegister(exchange, data);
                        break;
                    case "/backend/newcourse":
                        handleNewCourse(exchange, data);
                        break;
                    case "/backend/deletestudent":
                        handleDeleteStudentFromCode(exchange, data);
                        break;
                    case "/backend/newperiode":
                        handleNewPeriode(exchange, data);
                        break;
                    case "/backend/checkauthorization":
                        handlevalidAuthorzationToken(exchange);
                        break;
                    case "/backend/joincourse":
                        handleJoinCourse(exchange, data);
                        break;
                    case "/backend/maketeacher": 
                        handleMakeTeacher(exchange, data);
                        break;
                    case "/backend/deleteteacher":
                        handleDeleteTeacherRequest(exchange, data);
                        break;
                    case "/backend/requestteacher":
                        handleRequestTeacher(exchange, data); 
                        break;
                    default: 
                        handleThisRequestIsInvalid(exchange);
                        break;
                }
            }
            exchange.close();

        } else if (exchange.getRequestMethod().equalsIgnoreCase("GET")) {
                switch (exchange.getRequestURI().toString()) {
                    case "/backend/main":
                        handleGetMain(exchange);
                        break;
                    case "/backend/schedule":
                        try {
                            handleSchedule(exchange);
                        } catch (ParseException e) {
                            // TODO Auto-generated catch block
                            e.printStackTrace();
                        }
                        break;
                    case "/backend/getTeacherRequest":
                        handleGetTeacherRequests(exchange);
                        break;
                    case "/backend/getmystreak":
                        handleGetStreak(exchange);
                        break;
                    case "/backend/votes":
                        handleGetVotedSessions(exchange);
                        break;
                    case "/backend/activevoting":
                        handleVotingPeriods(exchange);
                        break;
                    case "/backend/scheduleteacher":
                        handleTeacherSchedule(exchange);
                        break;
                    case "/backend/getPeriodes":
                        handleGetPeriodes(exchange);
                        break;
                    case "/backend/currentactiveperiode":
                        handleGetCurrentActivePeriode(exchange);
                        break;
                    default: 
                        handleThisRequestIsInvalid(exchange);
                        break;
                }
            exchange.close();
        } else if (exchange.getRequestMethod().equalsIgnoreCase("OPTIONS")) {
            handleCORS(exchange);
        }

    }
 ```
## __Clarity__
The primary reason for why I think of this code as good is that its purpose and function is quite clear to a reader without excessive commenting. The comment at the top summarizes what it does and a reader can easily understand how the handle method is structured. Each path has a corresponding switch case which leads to a different handle method. It's simple and straight to the point.
## __Security__
As an added security/reliability perk, the code remembers to close the exchange each after the handle method finishes so as to not leave loose ends open and keeping the server traffic in check.
 # __Bad__
 The bad piece of code I would like to highlight is this one here from our project's backend.
 ```java
     /**
     * Method adds a user to the database
     * 
     * @param t   Integer that represents the type of the user
     * @param n   String that is the user's name
     * @param sn  String that is the user's surname
     * @param e   String that is the user's email
     * @param p   String that is the user's password
     * @param ti  Integer that is the user's timetable id
     * @param s   String that is the user's school
     * @param tok String that is the user's login token
     * @return Returns true or false if things went well or not.
     */
    public boolean makeUser(Integer t, String n, String sn, String e, String p, String s, String tok) {
        PreparedStatement stm = null;
        Connection con = database.getConnection();
        String sql = "INSERT INTO bruker (type, fornavn, etternavn, epost, passord, skole, token)"
                + "VALUES (?, ?, ?, ?, ?, ?, ?)";
        boolean ok = true;

        try {
            stm = con.prepareStatement(sql);
            stm.setInt(1, t);
            stm.setString(2, n);
            stm.setString(3, sn);
            stm.setString(4, e);
            stm.setString(5, Hashing.hashPassword(p));
            stm.setString(6, s);
            if (tok == null) {
                stm.setNull(7, Types.TIMESTAMP);
            } else {
                stm.setString(7, tok);
            }
            stm.executeUpdate();
        } catch (SQLException se) {
            se.printStackTrace();
            ok = false;
        } finally {
            try {
                stm.close();
            } catch(SQLException ex) {

            }
        }
        return ok;
    }
 ```
 ## __Age__
My first issue with this piece of code is the very apparent age of this code's commenting. It references the ``ti`` parameter that used to representing the timetable id collumn that a user had. This was removed in later iterations of the database, however as this method was one of the first to have been made, nobody returned at a later date to update this error in commenting. This is minor, but it is a flaw nontheless.
 ## __Parameters__
The parameters on the other hand are a more glaring issue. The way this method's parameters are named, being either one or two letters long make it very difficult to work out what each are without looking at the ``@param`` notation above. I noticed that while calling this method from elsewhere, and my IDE gave me prompts with the parameter names, I would always have to tab over to the actual method to read what each parameter was.
## __Security__
An even more severe issue is that the return boolean ``ok`` is initialized as true, and then is later modified to be false if a failiure were to happen. This would mean in theory, if an attacker could make it so the ``catch()`` block is not run during this method, the return value will remain true even though an error has occured which damages the integrity and reliability of the system. 

# __Refactoring__

## __Code before__
```java
/**
     * getIfTeacher checks if a student have applied to be a teacher or not
     * @param userid of the student to be checked
     * @param email of the student to be checked
     * @return true/false if it worked or not
     */
	public Boolean getIfTeacher(int userid, String email) {
        ResultSet result = null;
        Connection con = database.getConnection();
        PreparedStatement stm = null;
        String sql = "SELECT * FROM bruker " +
        "INNER JOIN brukerlerer " +
        "ON bruker.id = brukerlerer.brukerid " +
        "WHERE bruker.epost=?";
        boolean ok = false;

        try {
            stm = con.prepareStatement(sql);
            stm.setString(ROW1, email);
            result = stm.executeQuery();
            if(result.next()) {
                ok=true;
            }
        } catch(SQLException e) {
            e.printStackTrace();
        } finally {
            try {
                stm.close();
                result.close();
            } catch(SQLException e) {

            }
        }
		return ok;
	}
```
The issue we have with this code are minor but important.

## __Connection__
First and most important of all was that the database connection is never closed in the ``finally`` clause. This was due to our database implementation only ever establishing one connection to the database. If the connection would ever be closed and another method down the line would eventually call for the connection, they would get a closed object returend.
## __Unused parameter__
Second issue with this code was that it takes the user's ID as an argument, however it is never used in the method itself.
## __Misleading name__
Finally the name of the method was quite misleading as to what it did. ``getIfTeacher()`` makes it sound like it takes a user and returns true or false of whether they are a teacher or not, while in reality it checks if the user in question is currently applying to become a teacher. The comment at the top clarifies this, however the method name itself should be edited to make more sense.

## __Code After__
```java
/**
     * getIfTeacher checks if a student have applied to be a teacher or not
     * @param email of the student to be checked
     * @return true/false if it worked or not
     */
	public Boolean getIfApplyingForTeacher(String email) {
        ResultSet result = null;
        Connection con = database.getConnection();
        PreparedStatement stm = null;
        String sql = "SELECT * FROM bruker " +
        "INNER JOIN brukerlerer " +
        "ON bruker.id = brukerlerer.brukerid " +
        "WHERE bruker.epost=?";
        boolean ok = false;

        try {
            stm = con.prepareStatement(sql);
            stm.setString(ROW1, email);
            result = stm.executeQuery();
            if(result.next()) {
                ok=true;
            }
        } catch(SQLException e) {
            e.printStackTrace();
        } finally {
            try {
                stm.close();
                result.close();
                con.close();
            } catch(SQLException e) {

            }
        }
		return ok;
	}
```
Now the previouly mentioned issues have been adressed and updated accordingly. We had to refactor our DB implementation as well to allow for closing the connection.

## __Reflection on professionalism__
Taking this course has help make me more self aware of my bad habits while programming and the value of professionalism in projects. Even while you are working all by yourself on a rather small project, practices such as version control, smart commentating, active desting etc will make the development process easier for you and for anybody you might want to share it with in the future. I personally feel the content covered in this course should have been introduced earlier in the degree to set the habits straight by the point of everyone finishing their bachelor's thesises.

It has especially proved useful in group work, and then by extention helped prepare for the real work environmen, where working in teams is the norm and you are expected to be able to pick up from someone else's code at any point.