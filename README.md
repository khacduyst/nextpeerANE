nextpeerANE
===========
Adobe native extension that wrap Nextpeer SDK API, run on both iOS and Android from http://smappi.com
 
This game is using Signal (https://github.com/robertpenner/as3-signals) concept to fire events, some event return data object,please construct document api for more details 
 
 <b>Example on signal usage</b> :
 --------------------------------------------------------------------------
 			nextpeerExt.receivedCustomDataUnreliable.add(function(e:NPTournamentCustomMessage){
				var bys:ByteArray = e.message;
				trace("---- unreliable received ---------");
	 			trace(bys.readBoolean());
				trace(bys.readInt());
				trace(bys.readDouble());
				trace(bys.readUTF());
				trace("---- end unreliable received ---------"); 
			});
 --------------------------------------------------------------------------

<b>Some importance functions and flows</b>
 
 - initNextPeer(gameid:String) or initNextPeerWithSetting(gameid:String,style:String,alignment:String,position:String)
 - showDashboard();
 - gameStartedWithDetailsSignal
 - receivedTournamentStatusSignal
 - reportScore(score:Number)
 - sendCustomdata(dataArr:ByteArray) or sendCustomdataUnreliable(dataArr:ByteArray)
 - receivedCustomData and receivedCustomDataUnreliable
 - endGame(score:Number)
 - gameEndedSignal
 - receivedTournamentResultSignal

Because Nextpeer return data on GameResult (gameend) diffrence on iOS and Android so please read receivedTournamentResultSignal document to make sure you get right data 

 <b> simple how to use </b>
 --------------------------------------------------------------------------
 			public var nextpeerExt:NextpeerExtension = new NextpeerExtension();
 
 			var keyid:String = "";
			
			if(getOS()=="IOS"){
				keyid = "YOUR_NEXT_PEER_IOS_GAME_KEY";
			}else{
				keyid = "YOUR_NEXT_PEER_ANDROID_GAME_KEY";
			}
			
			nextpeerExt.initNextPeerWithSetting(keyid,NextpeerRankingDisplayStyle.SOLO,NextpeerRankingDisplayAlignment.VERTICAL,NextpeerRankingDisplayPosition.BOTTOM_RIGHT);
			
			//or just call nextpeerExt.initNextPeer(keyid);

 			nextpeerExt.gameStartedWithDetailsSignal.add(function(e:NextpeerTournamentContainer){
				trace(e.tournamentUuid);
				trace(e.tournamentRandomSeed);
				trace(e.tournamentTimeSeconds);
				
				playscreen.visible = true;
				homescreen.visible = false;
			});
			
			var byteArr:ByteArray = new ByteArray();
			byteArr.writeBoolean(false);
			byteArr.writeInt(15);
			byteArr.writeDouble(16.5);
			byteArr.writeUTF("this is a test message"); 
			TestNextpeer.instance.nextpeerExt.sendCustomdata(byteArr);
			TestNextpeer.instance.nextpeerExt.sendCustomdataUnreliable(byteArr);
			
			nextpeerExt.receivedCustomData.add(function(e:NPTournamentCustomMessage){
				var bys:ByteArray = e.message;
				trace("---- received ---------");
				trace(bys.readBoolean());
				trace(bys.readInt());
				trace(bys.readDouble());
				trace(bys.readUTF());
				
				trace("---- end received ---------"); 
			});
			
			nextpeerExt.receivedCustomDataUnreliable.add(function(e:NPTournamentCustomMessage){
				var bys:ByteArray = e.message;
				trace("---- unreliable received ---------");
				trace(bys.readBoolean());
				trace(bys.readInt());
				trace(bys.readDouble());
				trace(bys.readUTF());
				
				trace("---- end unreliable received ---------"); 
			});
			
			nextpeerExt.gameEndedSignal.add(function(){
				playscreen.visible = false;
				homescreen.visible = true;
				trace("game ended");
			});
			
			nextpeerExt.receivedTournamentResultSignal.add(function(e:NPTournamentEndDataContainer):void{
				trace("received result : "+e.playerRankInTournament);
			});
 
 
 --------------------------------------------------------------------------

Login Facebook (iOS setting)
===========

To allow login from facebook, you shold put this to your xml Please ge facebook SSO on Nextpeer dashboard and replace string start with fb and your app id in the sample code below 
--------------------------------------------------------------------------
        <key>CFBundleURLTypes</key>
	<array>
		<dict>
			<key>CFBundleURLSchemes</key>
			<array>
				<string>fbxxxxxxxxxxxxxxxxxxxxxxxx</string>
			</array>
			<key>CFBundleURLName</key>
			<string>your.app.id</string>
		</dict>
	</array>
--------------------------------------------------------------------------
	
Pushnotifications (iOS)
===========
Please use any push notification ane to allow your Adobe Air app using Push Notification, this one is good : https://github.com/freshplanet/ANE-Push-Notification

After that, when you receive token and pushdata, please pass to Nextpeer throug 2 functions below:

 - sendNotification2Nextpeer(str:String) : str is json string you get via push
 - registerDeviceToken(token:String)

Please make sure you have this in your app.xml to enable push working
--------------------------------------------------------------------------
	<Entitlements>
		<![CDATA[ 
	         <key>aps-environment</key> 
	         <string>production</string> 
	      	]]> 
	</Entitlements>
--------------------------------------------------------------------------

Pushnotifications and facebook login (Android)
===========
Android is more simpler, just add these config to your app.xml

--------------------------------------------------------------------------
	<uses-permission android:name=“android.permission.INTERNET” />	
	<uses-permission android:name=“android.permission.ACCESS_NETWORK_STATE” />	
	<uses-permission android:name=“android.permission.RECEIVE_BOOT_COMPLETED” />

		    
	<application>
		<activity android:name="com.nextpeer.android.NextpeerActivity" />   
		    	
		<activity android:name="com.nextpeer.android.facebook.LoginActivity"
				          android:theme="@android:style/Theme.Translucent.NoTitleBar"
				          android:label="@string/app_name" />
				 
		<service android:name="com.nextpeer.android.NextpeerPushService" />
		<receiver android:name="com.nextpeer.android.NextpeerBroadcastReceiver" >
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED" />
				<action android:name="android.intent.action.USER_PRESENT" />
			</intent-filter>
		</receiver>	
	</application>
--------------------------------------------------------------------------

