nextpeerANE
===========
Adobe native extension that wrap Nextpeer SDK API, run on both iOS and Android
http://smappi.com
 
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
  
 <b>Only use these API on iOS:</b>
 - sendNotification2Nextpeer
 - registerDeviceToken
 
 Because Nextpeer return data on GameResult (gameend) diffrence on iOS and Android so please read receivedTournamentResultSignal document to make sure you get right data

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

Login Facebook
===========

        <key>CFBundleURLTypes</key>
	<array>
		<dict>
			<key>CFBundleURLSchemes</key>
			<array>
				<string>fb192019987525706combizzonappfdotscfec</string>
			</array>
			<key>CFBundleURLName</key>
			<string>com.bizzon.app.fdots</string>
		</dict>
	</array>
	
	<Entitlements>
		<![CDATA[ 
	         <key>aps-environment</key> 
	         <string>production</string> 
	      	]]> 
	</Entitlements>

Pushnotifications
===========

phải đăng kí device với nextpeer : registerDeviceToken(token:String)
phải gửi data đi nextpeer khi nhận được push : sendNotification2Nextpeer(str:String)
Android thêm vào app.xml

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


