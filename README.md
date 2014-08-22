Adobe native extension that wrap Nextpeer SDK API, run on both iOS and Android
http://smappi.com
 
 
This game is using Signal (https://github.com/robertpenner/as3-signals) concept to fire events, some event return data object,please construct document api for more details 
 
 <b>Example</b> :
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
				//iOS
				keyid = "d9834a0e00be6c963bfbef66c9513cf5";
				trace("IOS");
			}else{
				//android
				keyid = "314f9feceb5058b0239d0b4b950ac0b5";
				trace("Android");
			}
			
			nextpeerExt.initNextPeerWithSetting(keyid,NextpeerRankingDisplayStyle.SOLO,NextpeerRankingDisplayAlignment.VERTICAL,NextpeerRankingDisplayPosition.BOTTOM_RIGHT);

 			
 
 
 --------------------------------------------------------------------------




nextpeerANE
===========
Nextpeer SDK port to Adobe Air (iOS and Android supported)


Events : We used Signal to dispatch events
===========

	+ gameStartedWithDetailsSignal (e: NextpeerTournamentContainer)
	+ gameEndedSignal
	+ cantInstallExt (có lỗi với nextpeer)
	+ receivedCustomData (e:NPTournamentCustomMessage) : e.message là ByteArray (chứa thông tin dữ liệu, gửi gì nhận đó)
	+ receivedCustomDataUnreliable : y như trên
	+ receivedTournamentStatusSignal (e:Array, mỗi phần tử kiểu NPTournamentPlayerResults), (mỗi s 1 lần gọi)
	+ receivedTournamentResultSignal (e:NPTournamentEndDataContainer)

Functions
===========

	+ initNextPeer(gameid)
	+ showDashboard()
	+ reportScore(score:Number)
	+ endGame(score:Number)
	+ reportForfeitForCurrentTournament() : quit game
	+ enableRankingDisplay(true/false) : Hiện ingame display hay ko
	+ sendCustomdata(e:ByteArray)
	+ sendCustomdataUnreliable(e:ByteArray)

iOS only functions

	+ registerDeviceToken(token:String)
	+ sendNotification2Nextpeer(str:String)

Sample code for send and receive custom data message
===========

Send

var byteArr:ByteArray = new ByteArray();

			byteArr.writeBoolean(false);
			byteArr.writeInt(15);
			byteArr.writeDouble(16.5);
			byteArr.writeUTF("this is a test message"); 
			TestNextpeer.instance.nextpeerExt.sendCustomdata(byteArr);

Receive

nextpeerExt.receivedCustomData.add(function(e:NPTournamentCustomMessage){

			var bys:ByteArray = e.message;
			trace("---- received ---------");
			trace(bys.readBoolean());
			trace(bys.readInt());
			trace(bys.readDouble());
			trace(bys.readUTF());
			
			trace("---- end received ---------"); 
		});

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


