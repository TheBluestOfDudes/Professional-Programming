# __Programming style guide__
### __Commenting__
We decided to comment our code in accordance with javadocs rather than lines of comments within the methods themselves. Every class would be preceded with a ``<h1>`` tag that is the title of the class, followed then by a ``<p>`` tag for the actual class description.

For methods we do not use the any such tags and instead prefase the method with a short summary of what the purpose of the method is and what it does, then followed up by a number of ``@`` symbols that explain different traints.
An ``@param`` explains the parameters the method accepts, ``@return`` explains what the method returns if anything and ``@exception`` names what type of exception the method throws.

If you want to read more about javadocs, then you can [look here](https://www.oracle.com/technical-resources/articles/java/javadoc-tool.html) for further reading.

### __Coding__
We decided early on in our project to agree upon a common standard for writing our code, so as to avoid things looking messy for any reader further down the line. We came to the following conventions.

There will only be line breaks between classes, methods, loops, if statements, switch cases, and right after variables are declared. Comments would be at the very top after javadoc conventions.

### __Example__

```
package com.KopifriOnsdag.bachelor;

import org.mindrot.jbcrypt.BCrypt;
/**
 *  <h1>Hashing</h1>
 *  <p>This class handles encrypting/decrypting of passwords through
 * hashing.</p>
 */
public class Hashing {
    /**
     * Hashes a password using the BCrypt library
     * @param password The password to be hashed
     * @return A hashed form of the inputted string
     */
    public static String hashPassword(String password) {
        String hashedPwd = BCrypt.hashpw(password, BCrypt.gensalt());
        return hashedPwd;
    }

    /**
     * Checks if a given password can be hashed to match the stored password
     * @param password The password to be checked
     * @param storedPassword The has of a password that is being compared to
     * @return True/False depending on whether there is a match
     */
    public static Boolean validatePassword(String password, String storedPassword) {
        if(BCrypt.checkpw(password, storedPassword)) {
            return true;
        }
        return false;
    }
}
```