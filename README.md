MultiThreadSQLiteOpenHelper
===========================

Description
-----------
An Android Multi Thread SQLiteOpenHelper

Enhanced SQLiteOpenHelper for Android applications where several threads might open and close the same sqlite database.

Instead of calling close method, threads ask for closing the database, thus preventing a thread from performing a query on a closed database.

If each thread asked for closing, then a close is actually performed.

Each activity or thread (ui-thread and user-threads) performs an open call on database when resuming, and asks for closing the database when pausing or finishing.


Usage
-----

- Create your own SQLiteOpenHelper by extending abstract class MultiThreadSQLiteOpenHelper
- Replace MyMultiThreadSQLiteOpenHelper by your own custom MultiThreadSQLiteOpenHelper in SimpleDbHelper.open(Context) method.
- You are now able to ask for opening or closing the database by calling SimpleDbHelper.INSTANCE.open(Context) and SimpleDbHelper.INSTANCE.close()
in onResume() and onPause() methods for Activities and when starting or stopping threads:

Activities running on UiThread:
	
	onResume() {
		...
		SimpleDbHelper.INSTANCE.open(this.getApplicationContext()); // ApplicationContext for long running operations
		...
	}
	
	onPause() {
		...
		SimpleDbHelper.INSTANCE.close();
		...
	}

User threads running in background:
	
	public void run() {
		try{
			SimpleDbHelper.INSTANCE.open(context); 
			...
		} catch(InterruptedException e) {
			...
		} finally {
			SimpleDbHelper.INSTANCE.close();
		}
	}


Example
-------

You can run this application and see what is happening for ActivityA and ActivityB in logcat
