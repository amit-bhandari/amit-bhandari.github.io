+++
title = 'Storing Java objects other than primitive types in Room Database'
author = 'Amit Bhandari'
date = 2017-07-13T11:58:59+05:30
draft = false
tags = ['Android', 'Room', 'Architecture Components']
description = 'Store java objects other than primitives in Room.'
+++

If you have been following Architectural components introduced in I/0 2017, you must already be familiar with the Room Persistence Library which provides you abstraction layer over SQLite to provide smooth database access and using SQLite with full potential. If you are not aware or don’t know how Room Database works, I would recommend reading this article which shows the very basic usage and implementation of Room Database in your android project.

By default, POJO class (called entity in Room DB) can have only primitive java data types which will be saved in respective columns by Room DB. Most of the time, this is not a problem because POJO classes generally have only primitive data fields. You can also use embedded tag to store object as nested columns in Room Database. But sometimes, we may need to store object as is in one column, so Type Converters comes to the rescue. If you do not use TypeConverter, you will come across this error.

> _error: Cannot figure out how to save this field into database._  
> _You can consider adding a type converter for it._

We will see how easily we can use Type Converter to store any object in Room by quickly modifying the User POJO from Googles official documentation to include one more field of ArrayList. Suppose we are storing database of users and you want to include their pet names as well while saving, you are gonna need ArrayList to save it (You don’t want your first dog to get bored while you go to work or read this, do you?)

---

Before we get started, put this dependencies in your gradle build file.

{{< highlight groovy >}}
compile "android.arch.persistence.room:runtime:1.0.0-alpha3"
annotationProcessor "android.arch.persistence.room:compiler:1.0.0-alpha3"
compile 'com.google.code.gson:gson:2.6.2'
{{< /highlight >}}

First 2 lines for including Room DB in our project and 3rd one is Gson which we will use to convert our object to String.

---

Our modified User.java looks something like this. Even though you can’t see any getter or setter method here, you need to include them or make the fields public for Room to access it.

{{< highlight java >}}

@Entity  
public class User {
    @PrimaryKey
   private int uId;
   private String uName;
   private ArrayList<String> uPets = new ArrayList<>();
   public User() {
   }
   public User(int id, String name, List<String> pets){
      this.uId = id;
      this.uName = name;
      this.uPets.addAll(pets);
   };
   //getters setters removed for brevity
}

{{< /highlight >}}   

User DAO stays more or less the same. You can include methods to fetch particular user from user id or name, I will leave that as an exercise for you.

{{< highlight java >}}

@Dao
public interface UserDAO {
   @Insert(onConflict = REPLACE)
   void insertUser(User user);
   
   @Query("SELECT * FROM User")
   List<User> getUsers();
   
   //include any methods to fetch specific user
   //@Query("SELECT * FROM User")
}

{{< /highlight >}}

And our UserDB will also stay the same except small difference.



{{< highlight java >}}

@Database (entities = {User.class},version = 1)
@TypeConverters({Converters.class})
public abstract class UserDB extends RoomDatabase {
   public abstract UserDAO userDAO();
}

{{< /highlight >}}

Noticed that @TypeConverters annotation we provided along with @Database annotation, this will be the class which will tell Room how to convert ArrayList object to one of SQLite data type. We will implement methods to convert ArrayList to String for storing it in DB and String back to ArrayList for getting back original User object.

> *Type Converter specifies additional type converters that Room can use. The TypeConverter is added to the scope of the element so if you put it on a class / interface, all methods / fields in that class will be able to use the converters.*

---

Without further delay, we will see how our type converter looks in our case.


{{< highlight java >}}

public class Converters {
   @TypeConverter
   public static ArrayList<String> fromString(String value) {
      Type listType = new TypeToken<ArrayList<String>>() {}.getType();
      return new Gson().fromJson(value, listType);
   }
   @TypeConverter
   public static String fromArrayLisr(ArrayList<String> list) {
      Gson gson = new Gson();
      String json = gson.toJson(list);
      return json;
   }
}

{{< /highlight >}}

As you can see, we have 2 methods in TypeConverter.

1. *public static String fromArrayLisr(ArrayList<String> list)* : This method takes our arraylist object as parameter and returns string representation for it so that it can be stored in Room Database. Very simple and easy way to convert any object to string is converting it into its JSON equivalent. Just creating Gson object and calling toJson method with our object as parameter is enough.

2. *public static ArrayList<String> fromString(String value)* : While reading data back from Room Database, we get JSON form of our arraylist which we need to convert back. We will use Gson method fromJson by providing JSON string as parameter. But while converting back, we also need to provide the class of original object (in our case, arraylist), but providing arraylist is not enough here as Gson will not be able know what kind of list it has to form. That’s why we used Type to provide the type of list we want Gson to form for us from the JSON string.

And that’s it, converter is ready to be used. We have mentioned this converter in UserDB class which will be automatically used by Room Database to convert arraylist to string and vice versa. I hope you found this article helpful, if yes, be sure to leave your comment. In next article, I will write about migrating current SQLite implemented using SQLiteHelper classes to Room Database.

You can find the sample here at [GitHub](https://github.com/amit-bhandari/RoomSamples).

---

This article was originally written on medium [here](https://medium.com/@amit-bhandari/storing-java-objects-other-than-primitive-types-in-room-database-11e45f4f6d22)

---