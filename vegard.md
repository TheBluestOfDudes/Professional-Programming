# __Good code__
```js
import React from 'react';
import {Component} from 'react';
import {Navbar} from 'react-bootstrap';
import StudentNav from './Student/StudentNav';
import TeacherNav from './Teacher/TeacherNav';
import AdminNav from './Admin/AdminNav';
import AuthorizationNav from './All/AuthorizationNav';

/**
 * <h1>Navigation</h1>
 * <p>This class have takes care of rendering the navigations for each user</p>
 */
class Navigation extends Component {

    render() {
        return (
```
```html
                <Navbar collapseOnSelect expand="lg" bg="dark" variant="dark" className="justify-content-between">
                    <Navbar.Brand href="#home">KopiFriOnsdag</Navbar.Brand>
                    <Navbar.Toggle aria-controls="responsive-navbar-nav" />
                    <Navbar.Collapse id="responsive-navbar-nav">
                        { this.props.type === "Student" && this.props.loggedIn ? 
                        <StudentNav/> : ""
                        }
                        { this.props.type === "Teacher" && this.props.loggedIn ?
                        <TeacherNav/> : ""
                        }
                        { this.props.type === "Admin" && this.props.loggedIn ?
                        <AdminNav/> : ""
                        }
                        <AuthorizationNav type={this.props.type} email={this.props.email} loggedIn={this.props.loggedIn} ifTeacher={this.props.ifTeacher}/>
                    </Navbar.Collapse>
                </Navbar>
                
        );
    }
}
```
```js
export default Navigation;
```

Code over we can look at an example i mean is good code. The reason for this is because its easy to maintain and refactor later when needed. This code is taken from our frontend and are made with the single resposibillity principle from the solid design principle. This means that every component we have made in react for our frontend only have one responisbillity. En example is this Navigation only responsibillity is rendering the right component for the right user. This means if it will be added other roles than student, Admin and Teacher is very easy to do that and send them to right navigationbar. This is a real possibility because this a school system, Maybe later want to add a headmaster role in the system. Other example is if we want to change the students navigation bar in the Studentnav component. We wouldnt change anything in this component, we seperate the code so it easier to maintain and would not be a big cluster of code that are depending on each other in one big file. We are also using bootstrap to make the navigation bar looking nice even though i have no background in design. Bootstraps helps us with this bit with placing the navbar on recommended places based og design patterns.  

# __Bad code__

```java
 /**
     * Getting the votingtimes from Email and send them back 
     * @param email of the students joined course
     * @param id of the student
     * @return JSONObject of codes from courses that have a open voting
     */
    public JSONObject getVotingTimeFromEmail(String email, int id) {
        ResultSet result = null;
        Connection con = database.getConnection();
        PreparedStatement stm = null;
        String sql = "SELECT kode FROM brukerfag WHERE bruker=?";

        List<String> codes = new ArrayList<>();

        try {
            stm = con.prepareStatement(sql);
            stm.setInt(1, id);
            result = stm.executeQuery();
            while (result.next()) {
                codes.add(result.getString("kode"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }

        JSONObject send = new JSONObject();
        for (String code : codes) {
            sql = "SELECT dato FROM stemme WHERE fagkode=? AND elev=?";
            try {
                stm = con.prepareStatement(sql);
                stm.setString(1, code);
                stm.setInt(2, id);
                result = stm.executeQuery();

                List<Date> elements = new ArrayList<>();
                while (result.next()) {
                    elements.add(result.getDate("dato"));
                }
                send.put(code, elements);
            } catch (SQLException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }

        }
        try {
            result.close();
            stm.close();
            con.close();
        } catch(SQLException ex) {

        }
        return send;
    }
```
Its several reason i think this code is bad in general. This code will be refactored in the future. The first i want to say that i dont need to have two sql statements here but combined them to one sql stament and use an inner join to check get the same result. In the teory i could half the code in this function. In this code we will loop through all the codes and do a lot of database calls that is not neccessary, that will both eat resources and performance to fix this i would use the inner join and we would only need to run one sql query to the database to get the same information. This way would been way better because going when the api gets bigger the reduced datbase calls can make other request go faster. In general if this was going out to be deployed on any school the e.printstacktrace should be deleted and changed to a java logging class. With this logging class we can restrict what messages that are getting sendt to the console. Instead of having the 1 and 2 in setString and setInt, we could refactor the code to give us an constant instead like ROW1, so when someone that have never seen this type of code can better understand this function.

# __Refactored code__
## Before
```java
package com.KopifriOnsdag.bachelor;

import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Connection;

/***
 * <h1>Class connects to MySQL database and returns the connection</h1>
 */
public class DB {
    // Database information
    private static final String USER = "user";
    private static final String PASS = "test";
    // Database connection
    private static Connection connection = null;

    /***
     * Constructor opens connection to the MySQL database
     * 
     * @throws Exception Exception if the connection to the database fails
     */
    public DB() {
            try {
                Class.forName("com.mysql.cj.jdbc.Driver");
                connection = DriverManager.getConnection("jdbc:mysql://mysql:3306/db?" +
                "user="+USER+"&password="+PASS+"&serverTimezone=Europe/Oslo");
            } catch (SQLException | ClassNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
            }
    }

    /***
     * Method that returns the database connection
     * @return The connection to the database
     */
    public Connection getConnection(){
        return connection;
    }
}
```
The reason we had to refactor is this code was because the behaviour that was intended did not happend. Whenever we was communication to the database to queries as seen in the bad database example. We want to close the connection to the database so it doesnt hang from earlier connections. So we had to close the connection as seen above. That means in ths current version of the code it would been closed for a new request to the database, and therefore we had to refactor the code as seen below.

## After
```java
package com.KopifriOnsdag.bachelor;

import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Connection;

/***
 * <h1>Class connects to MySQL database and returns the connection</h1>
 */
public class DB {
    // Database information
    private static final String USER = "user";
    private static final String PASS = "test";
    // Database connection
    private static Connection connection = null;

    /***
     * Constructor opens connection to the MySQL database
     * 
     * @throws Exception Exception if the connection to the database fails
     */
    public DB() {
            try {
                Class.forName("com.mysql.cj.jdbc.Driver");
                connection = DriverManager.getConnection("jdbc:mysql://mysql:3306/db?" +
                "user="+USER+"&password="+PASS+"&serverTimezone=Europe/Oslo");
            } catch (SQLException | ClassNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
            }
    }

    /***
     * Method that returns the database connection
     * @return The connection to the database
     */
    public Connection getConnection(){
        try {
            if(connection.isClosed()) {
                connection = DriverManager.getConnection("jdbc:mysql://mysql:3306/db?" +
                "user="+USER+"&password="+PASS+"&serverTimezone=Europe/Oslo");
            }
        } catch(SQLException e) {
            e.printStackTrace();
        }
        return connection;
    }
}
```
Now when connection get closed we check if the connection is closed or not and then depending on the answer make a new connection to the database. The problem got solved by this change. Now we did not have 50 hanging database connection in our application and prevented som crashes we had gotten because of this reason.

## Professionalism

I think in the profesionalism part i have gotten better while we have been doing the bachelor thesis. Example of that is that i started testing function and class immediately after making them, instead of doing it later. This is a way better for both my group and myself. This is because tests make a better understanding of how a function may work, instead of only reading the code. At the same time you have a test incase it breaks later and you know it have worked before. In general it has been a good process and i would carry this new knowlegde with me to the next projects i will work with other people. Other aspect i have tried to give my group members help as much as i can with inviting them to discord calls to discuess different decitions we wanted to do. 