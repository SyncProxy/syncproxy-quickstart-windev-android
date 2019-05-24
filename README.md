# SyncProxy quick start guide for WinDev Mobile for Android  

This quick start tutorial will show you to how to setup a Windev Mobile application for Android embedding its own database together with **SyncProxy** client. Used with online **SyncProxy** server (www.syncproxy.com), this is the quickest way to create applications that will synchronize bidirectionally, reactively in realtime with your backend database (MySQL, SQL Server, MongoDB...), and also work perfectly when offline.   
  

## Architecture  

The **SyncProxy** client targets WinDev Mobile applications for Android with an embedded SQLite database that provides offline capabilities. By adding the **SyncProxy** client to your WinDev Mobile application, you will turn your offline app into a powerfull online application with bidirectionnal reactive (realtime) sync that will work perfectly with or without connection. You will not need develop any specialized webservices, since the **SyncProxy** server enables to define sync profiles directly online on www.syncproxy.com which acts as a gateway between your app and your backend database.

## Before starting  

Go to www.syncproxy.com, register and log-in as a proxy administrator (it's free !), then create and configure your proxy to synchronize with your backend database.  
  

## How to upgrade an existing WinDev Mobile Android application for SyncProxy reactive sync  

To get your existing WinDev Mobile application for Android ready with the **SyncProxy** client:  

 1. Download the **SyncClient.AAR** library for Android from [https://github.com/SyncProxy/sync-client-android](https://github.com/SyncProxy/sync-client-android) (release folder) and copy it into the *Android* subfolder of your WinDev Mobile project.  
 2. Import the library to your project (6th page "Libraries" of Android app generation options)
 3. Create a java global procedure **StartSyncClient** that will start database  observation and display the sync button:

  ```java  
import com.syncproxy.syncclient.SyncClient;
public static void StartSyncClient()
{
	SyncClient.startObservation(getCurrentActivity(), "{'proxyId':'<your proxy Id>', 'connectorType':'SQLite', 'dbName':'<local db name>'}" ).showSyncButton(getCurrentActivity());
}
  ```  
>Replace *dbName* attribute with the name of the local database . You get your *proxyId* on my.syncproxy.com after creating the sync proxy.

 4. In the code of your main window, simply call the StartSyncClient() function from the *End of initialization* handler.

````java  
StartSyncClient()
````  
Compile and launch your app: the Sync button should now appear on top of the main window of your application:  

  ![sync button](https://raw.githubusercontent.com/syncproxy/syncproxy-quickstart-android/master/sync-icon.png)

and when sync is launched, the login prompt should popup:  
  ![login prompt](https://raw.githubusercontent.com/syncproxy/syncproxy-quickstart-android/master/login-prompt.png)
  
You are now ready to sync !  Any changes made to your embedded database are sent in realtime to your backend database and *vice-versa*. When offline, all changes made are marked temporarily, and synched when going back online, bidirectionnally.  

## How to display sync button on other windows
If you want to display the sync button in other windows of your application, simply create the following java global procedure and call it from the *End of initialization* handler of each window:  
````java  
import com.syncproxy.syncclient.SyncClient;
public static void ShowSyncButton()
{
	SyncClient.showSyncButton(getCurrentActivity());
}
```` 
## How to trigger an update of the display after sync
If your app needs updating its display after data have been modified, this can be done simply by passing an *onSyncEnd()* handler (java Runnable) to the SyncClient library's *showSyncButton()* function.

**Example:**
```
	Runnable onSyncEnd = new Runnable(){public void run(){
	  GWDPMyProject.ms_Project.mWD_MyWindow.fWD_refreshMyWindow();
	}};
	SyncClient.startObservation(getCurrentActivity(), "{'proxyId':'<your proxy Id>', 'connectorType':'SQLite', 'dbName':'<local db name>'}" )
	.showSyncButton(getCurrentActivity(), onSyncEnd);
```
in the above example, the local (WLanguage) procedure *refreshMyWindow()* of MyWindow is called after each successful synchronization.
>Since we're coding within a **java** global procedure, the names of your app's objects (project, windows, controls...) used in *onSyncEnd()* handler are the names **created by the WinDev java generator** (see */Android/Compile/\*.java* files).


## How to trigger different updates for each window after sync
If each window requires different updates, you can create several global procedures such as *ShowSyncButtonWin1()*, *ShowSyncButtonWin2()*, each of which using a different *onSyncEndWin1()*, *onSyncEndWin2()* onSyncEnd handlers.

## How to check synched data  

Since you certainly started testing your WinDev Mobile app for Android inside an Android Emulator, good chances are that you have already installed Android Studio. If so, you can explore and debug your WinDev generated Android app and its database, using regular Android Studio tools, as described in the [SyncProxy quick start guide for Android](https://github.com/SyncProxy/syncproxy-quickstart-android).

## Database schema management  

The **SyncProxy** sync client will create the embedded database for you !  You won't need to worry about the tedious schema management (create tables, created columns, etc.). Every upgrade made to your backend database will apply to the embedded database during the synchronization.
>All you have to do is adapt the analysis and the schema of your WinDev Mobile application to stay in tune with the database structures.
  
## Notes

 - In SQLite connection settings (WinDev app's analysis), the database files must imperatively be stored within the app's default folder: this is where the database will be managed by the sync client.
- If you encounter some issues with HFSQL libraries during Android app generation, you may try to disable them.
- Also you may need to configure the minimal version of Android to 7.0 (10th page of Android app generation options) to 
## Link  

To access **SyncProxy** administration to setup your sync proxy and connect to your backend database, go to www.syncproxy.com  