# __Good__
A piece of code I believe to be good is this example here.
 ```java
 
 ```

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