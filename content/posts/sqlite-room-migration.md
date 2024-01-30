+++
title = 'Migrating existing SQLite implementation to Room'
author = 'Amit Bhandari'
date = 2017-07-14T11:58:59+05:30
draft = false
tags = ['Android', 'Room', 'Architecture Components', 'Java']
description = 'Everything you need to know about migrating existing SQLlite to Room database.'
+++

[Room](https://developer.android.com/topic/libraries/architecture/room.html) is a persistence library introduce by Google in this year I/0. 
Room makes it extremely easy to work with SQLite in your android application and worth giving a shot if you are using SQLiteOpenHelper as of now for handling data in your app. 
Migrating your current implementation to Room is, surprisingly, very easy. 
Google has provided samples for this here, but I personally think it’s too much code for such simple task and it can be explained easily. 
I explain below few simple steps for migrating your current implementation to Room DB and I hope you will find it helpful, if you do, share it and let other people know about it.

---

## SQLiteOpenHelper implementation:
[SQLiteOpenHelper](https://developer.android.com/reference/android/database/sqlite/SQLiteOpenHelper.html) is a helper class which manages database creation and version management. 
All the CRUD operations of database we have to implement by sub classing SQLiteOpenHelper. 
You can find more info about how it is done here. 
We will migrate user database currently implemented using SQLiteOpenHelper. Current implementation looks like this.

{{< highlight java >}}
public class User  {
   private int uId;
   private String uName;
   private String uContact;
   public User() {
   }
   public User(int id, String name, String number){
      this.uId = id;
      this.uName = name;
      this.uContact= number;
   };
   //getters setters left out for brevity
}
{{< /highlight >}}

All the users we have to store in SQLite db and for that we will use helper class UserDbHelper sub classed from SQLiteOpenHelper.

{{< highlight java >}}
public class UserDbHelper extends SQLiteOpenHelper {
   private static final int DATABASE_VERSION = 1;
   // Database Name
   private static final String DATABASE_NAME = "userDB";
   // user table name
   private static final String TABLE_USERS = "users";
   // user Table Columns names
   private static final String USER_ID = "user_id";
   private static final String USER_NAME = "user_name";
   private static final String USER_PH_NO = "user_contact";
   public UserDbHelper(Context context) {
   super(context, DATABASE_NAME, null, DATABASE_VERSION);
   }
   @Override
   public void onCreate(SQLiteDatabase db) {
      //create the table if not yet created
      String CREATE_USER_TABLE = "CREATE TABLE " + TABLE_USERS + "("
      + USER_ID + " INTEGER PRIMARY KEY," + USER_NAME + " TEXT,"
      + USER_PH_NO + " TEXT" + ")";
      db.execSQL(CREATE_USER_TABLE);
   }
   @Override
   public void onUpgrade(SQLiteDatabase db, int i, int i1) {
      // Drop older table if existed
      db.execSQL("DROP TABLE IF EXISTS " + TABLE_USERS);
      // Create tables again
      onCreate(db);
   }
   public void addUser(User user){
      //Add new user to database
      SQLiteDatabase db = this.getWritableDatabase();
      ContentValues values = new ContentValues();
      values.put(USER_NAME, user.getUName()); // Contact Name
      values.put(USER_PH_NO, user.getUContact()); // Contact Phone
      values.put(USER_ID, user.getUId());
      // Inserting Row
      db.insert(TABLE_USERS, null, values);
      db.close(); // Closing database connection
   }
   public User getUser(int id){
      SQLiteDatabase db = this.getReadableDatabase();
      Cursor cursor = db.query(TABLE_USERS, new String[] { USER_ID,
         USER_NAME, USER_PH_NO }, USER_ID + "=?",
      new String[] { String.valueOf(id) }, null, null, null, null);
      if (cursor != null)
      cursor.moveToFirst();
      User contact = new User(Integer.parseInt(cursor.getString(0)),
      cursor.getString(1), cursor.getString(2));
      return contact;
   }
}
{{< /highlight >}}

Now we can add users to database like this.

{{< highlight java >}}
//get database instance
UserDbHelper userDbHelper = new UserDbHelper(this);
//add couple of users
userDbHelper.addUser(new User(1,"Hulk","11-445-9999"));
userDbHelper.addUser(new User(2,"Dominic","11-445-9999"));
{{< /highlight >}}

And fetch the users using this.
{{< highlight java >}}
User extractedUser = userDbHelper.getUser(1);
{{< /highlight >}}  


*As you can see, we have to write queries to put and fetch data in table ourselves using ContentValues and cursors. 
This kind of code is highly prone to errors and errors can be difficult to find as well. 
Also there is not compile time check involved in above implementation which can produce potential runtime errors and termination.*

Room helps you achieve database abstraction through Data Access Objects and also provides compile time query checks so as to minimize potential runtime failures. 
Win win for developers!

---

## Migrating to Room:
I assume that at this point, you know the basics of how Room works and have tried it out. 
If not, I would recommend you go through this tutorial to get started. 
For migrating our current implementation of users database, we will update our POJO class first. 
Lets add @Entity annotations to User class as this class will be mapped to table in our database. 
It is mandatory to define at least one primary field in Room.

{{< highlight java >}}

@Entity
public class User  {
   @PrimaryKey
   private int uId;
   private String uName;
   private String uNumber;
   public User() {
   }
   @Ignore
   public User(int id, String name, String number){
      this.uId = id;
      this.uName = name;
      this.uNumber= number;
   };
   //add all the getters and setters here
}
{{< /highlight >}}

Let’s create Data Access Object (DAO) for exposing methods which will be used by application for accessing the data back and forth from database. 
Our DAO implementation will be very easy, it will only be 2 method, one to add user to database and one to read all users from database.

{{< highlight java >}}
@Dao
public interface UserDAO {
   @Insert(onConflict = REPLACE)
   void insertUser(User user);
   @Query("SELECT * FROM User")
   List<User> getUsers();
}
{{< /highlight >}}


As you can see above, how simple implementation we provided for Data Access Object. 
Compare it with the methods we used in SQLiteOpenHelper class for carrying out same operations. 
For adding, updating or deleting row(s) in table, we don’t even have to form queries, Room will do it for us. 
Only for reading data, we need to use @Query annotation and provide it with query unlike SQLiteHelper where we have to deal with cursors and all of boiler plate code. 
Remember, more the code, more the bugs!

We will get DAO object instance from RoomDatabase class. 
RoomDatabase class will be the abstract class with only one method as shown below. 
We have to mention version number for DB in this class. 
As we are migrating Database from SQLiteHelper to Room, version number needs to be more than SQLiteHelper db version number. 
We have used version 1 for our SQLiteHelper Database, so we will user version 2 for Room.

{{< highlight java >}}

@Database (entities = {User.class},version = 2)
    public abstract class UserDB extends RoomDatabase {
    public abstract UserDAO userDAO();
}

{{< /highlight >}}

And we are done with our Room Database implementation and its time to see it in action. 
While creating RoomDatabase, we have to provide migration information which we will see in next part.

---

We need *Migration* class instance while building Room Database from database builder.
Migration class constructor takes 2 versions as parameter, one is the version from which it needs to migrate (version 1 in our case) and second is the version to which it is going to migrate (version 2 in our case). 
It provides one method migrate(SupportSQLiteDatabase db). [SupportSQLiteDatabase](https://developer.android.com/reference/android/arch/persistence/db/SupportSQLiteDatabase.html) which is called by Room while migrating the Database with db instance as parameter. 
Here is the place where we can make changes (if any) to db schema or db data. 
If we do not want to make any changes, and just want to migrate, we leave the implementation empty.
This is how we create Room Database.


{{< highlight java >}}
final Migration MIGRATION_1_2 = new Migration(1, 2) {
   @Override
   public void migrate(SupportSQLiteDatabase database) {
   // Since we didn't alter the table, there's nothing else to do here.
   }
};
//create db
final UserDB userDB = Room.databaseBuilder(this
   , UserDB.class
   , "userDB")
   .addMigrations(MIGRATION_1_2)
   .build();
//inserting and accessing data using DAO
//this operations needs to be performed on thread other than main thread
userDB.userDAO().insertUser(new User123(5,"Wowman", "888888888"));
userDB.userDAO().insertUser(new User123(6,"Captain", "888888888"));
User user = userDB.userDAO().getUsers().get(0);

{{< /highlight >}}

And that is pretty much it. We are done with our changes and executing this will migrate our db successfully to room.

You can get the source code mentioned here on my [Github](https://github.com/amit-bhandari/RoomSamples) here.

---

**But while migrating db, you need to make sure of couple things, otherwise you may face issues.**

1. You have to make sure column names mentioned in SQLiteHelper class and your POJO class should match, otherwise migration will not be successful. 
For e.g. suppose column name for id field of user is ‘user_id’ in SQLite open helper class, either you have to name your id variable in POJO class ‘user_id’ or explicitly mention column name of id field by using @ColumnInfo annotation like this

{{< highlight java >}}
@ColumnInfoname(name="user_id")
private int uId;
{{< /highlight >}}


2. You have to make sure that table names also matches. 
By default, room will assign table name as name of entity class (‘user’ in our case), but we can change this by mentioning table name while annotating entity class like this *@Entity( tableName = “users” )*

---

This article was originally written on medium [here](https://medium.com/@amit-bhandari/migrating-existing-sqlite-implementation-to-room-d05bb8dada7e)

---