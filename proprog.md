# Team members
- Abubakar Ahmed Yusuf
- Vegard Skaansar
- Odd Arne Hagelund (Not part of professional programming)
# Group discussion

## __Language__
For this project we chose to use Java for developing the backend, and Javascript for the frontend.

### __Java__
We chose Java over other server side languages such as PHP for largely familiarity. This being our bachelors project, we wanted to be as effective as we could possibly be while working on it. When we researched the languge's performance, we learned that general purpose language like Java is actually considerably slower than a dedicated server-side scripting language like PHP. However, we all made a decision early on, that group cohesion was more important. Additionally, our expirience with Java had primarily up until that point been been focused on desktop development, so we wanted to use this opportunity to learn about the server focused side of the language.

Some points in java's favor is the tools and resources surrounding the languages. Oracles documentation of classes are easy to read, and there is a mountain of content and tutorials out there on how to do things in Java. We can also create our own code's documentation through tools like Javadocs, and can manage our dependencies very seemlessly through a system like Maven. Dependency management in particular was something we found to be more tedious with languges such as Go and PHP that we would rather not have to deal with.

We did find Java's documentation lacking in regards to server security. One example is dealing with cross site scripting (XSS). We had to first have the browser send a request with a header that tells the server where the request is coming from, then if the source is an approved of one, the browser will be allowed to send its actual request. PHP has native methods for working against XSS attacks, such as ``htmlspecialchars()``, which changes symbols such as double quotes, ampersands and etc into strings that will make the XSS code unrunable. PHP still does the same operations as we had to do in java, though we found PHP's documentation on how this is implemented to be more clear compared to Java.

### __Javascript__
Our choice of Javascript is because we wanted to build our application in React.js. Javascript is already the defacto language for web development, and React.js was a very powerful library for building flexible component based web pages. Our group only had passive knowledge about React.js prior to this project, our expirience actually largely being in the Polymer library, however React's relative simplicity was much more intuitve and attractive to us.

Both libraries are based around building web pages through custom web components that are defined as Javascript classes with element properties stored as fileds within the class. The HTML is rendered through a method within the component class. This same general structure is the same in both libraries, which made the transition quite seemless. A point in React's favor however is a reduced complexity for little lose in functionality.

Polymer makes use of technoligies such as shadow dom and two way data binding with its properties. This does allow for a very high level of flexibility at the hands of a skilled developer, though the added complexity also makes it harder for a begginer to get into. React keeps things simpler with just using HTML, and having one way databinding instead. The power of two way databinding is that it's less restrictive to the developer, however it also makes it ambiguious of where changes of data originated from, which makes debugging more diffuclt. While with React's one way binding, it will always be known where a change came from.

## __Process/Communication control__
The group maintained contact through a variety of different channels.

### __Discord__
Discord was our primary platform for notifying group members of things, such as not being able to make it to a meeting for a week or whatnot, as well as sharing either screenshots or livestreaming our work to get help from the group. After the pandemic started, Discord also became our primary platform for group meetings in general as well.

### __Group meetings__
We held meetings every other day of the way on campus, and met with our project advisor once per week on top of this. During these meetings we would work and talk to each other face to face, showcased what he had made, as well as agreed on what the plan of action for the next couple of days would be. These meetings eventually moved over to discord due to the pandemic.

### __Trello__
We started our project adhering to our plan of running an incremental-sequential development model, trello being used as a Kanban board for each increment we were currently working for. We tagged the various task in trello with hashtags, however we eventually migrated this over to the issue tracker of our git repository. The primary reason behind this change is that we found trello to become increasingly messy with all the hashtags, while simply posting commits with a # that was inside the issue tracker proved far easier for everyone involved to understnad and do.

Instead of having to go to an external sight and see that a task with the #5 had been moved, and then would have to look up what that # corresponded to, we could simply look at the repository, see that a commit with the #5 was submitted then just look in the issue tracker to know what it meant.

## __Version Control__
We made use of two methods for handling version control in our project. Ordinary git repository and then for a while we had things divided into branches.

### __Branches__
Early on we had tried to work with using branches in our repository, each branch corresponding to a different feature being developed by one of us. For the first increment of our project, which was to set up the groundwork that we would later expand upon, such as making a basic frontend, connecting to a database and handling user profiles. Things got worse however as the project became bigger.

Due to group members' general inexpirience with working with branches, we encountered merging issues and several cases of a group member being on an out of data version when they attempted to merge with master, which ended up removing things. We decided as a group to fix this by doing away with bracnhes and instead organizing our repository by way of issues.

### __Git__
In the issue tracker we defined a series of hastags that corresponded to a theme for commits. Commits to master that had to do with database functionality, would all be prefixed by the relevant hashtag, and commits had to do with testing would be prefixed by another. This removed the confusion and headaches that came with trying to use branches, while still giving us control of whatwas being developed and and what was being merged in.

## __Coding style and commenting__
The guide can be found here [here](styleguide.md)

## __Use of libraries__
When it came to usage of libraries we apporached it from the point of view of having as few external dependencies as possible, however we also had to identify when this was inpractical. For instance with security protocols such as hashing of passwords, using of libraries was natural as they have in all liklihood been rigorously tested to work properly. Using MySQL Connector to connect our server to our database, and using React.js to build our frontend application all make these essential tasks much more stream lined and are tried and tested by many already. For things that were not absolutely vital however, we prioritized implementing the functionality ourselves to maintain the most amount of individual reliablity for our system.

Our project was spesifically to develop a prototype for a wider system that could be further polished into the future, and then eventually launched commercially. This helped guide our choices of libraries, as we wanted to avoid the application being undeployable due to lisenceing issues with third party dependencies. 

## __Code review__
We chose to do this project using the incremental-sequential development model. Each increment consisted of a three week development period wherein the first two weeks was for impleneting the functionality for that particular increment, and the final week was for peer reviewing each others' work.

During these reviews, group members would look through each others' codes and look out for any errors in coding style, syntax, efficency and logic. Feedback would be given and then improvements would be made. 

## __Individual review__
[Abu](abu.md)

[Vegard](vegard.md)