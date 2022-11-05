# This game is going to be totally broken in a bit (tutorials and knowledge inside)
<hr>
<div id="post_message_3523020">First-up, been reading left and right and it seems some people are having a hard time &quot;reading&quot; or &quot;talking&quot; in UE4 language. Not to mention the poor way in which they're transmitting this information to the public. Before this game, I learned a lot about UE4 the hard and tedious way: download the source code, compile the editor, compile a legacy game (ShooterGame) with debug symbols and f*ck around. That's how I also learned of the internal framework, how objects intertwine and a sh!t ton out of the public source-code. So those of you who think hacking UE4 games revolves around dumping Objects and SDKs.. know that it's not about that. That is merely the last part you'd get to, after a quite interesting learning path. So no, it's all about learning and getting to know Unreal Engine's internal structure. Once you master that, you're good to go. Why? Because every other game will revolve around same principles.<br />
<br />
Saddle up, it's going to be a BIG post.<br />
<br />
--<br />
<br />
<b>HOW TO BUILD THE ENGINE</b> (for local testing, learning, etc.)<br />
<br />
You can get the source code from Epic's github, after <b><u>signing up</u></b> and checking their rules below:<br />
<br />
<img src="https://i.imgur.com/miZi7h4.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Download the <a rel="nofollow" href="https://github.com/EpicGames/UnrealEngine/releases/tag/4.26.2-release" target="_blank">4.26.2-release</a> locally. Instructions on how to build the Editor on MSVS2017 are <a rel="nofollow" href="https://github.com/EpicGames/UnrealEngine/tree/4.26" target="_blank">here</a>. (&quot;Getting up and running&quot; section). You can skip step 1, as you've already downloaded the source-code. And step 2, if you've already installed MSVS2017. From 3 to 6, that's your aim.<br />
<br />
Why 4.26? Because that's what ToF is using:<br />
<br />
<img src="https://i.imgur.com/IG5Kre8.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Then you want to get the data for a DEMO GAME which you can conveniently find in the Epic Games Launcher, in the <b>Vault</b>:<br />
<br />
<img src="https://i.imgur.com/mJpiFif.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
I personally prefer and use the <b>ShooterGame</b> project.<br />
<br />
Things to be aware of:<br />
<br />
1) MSVS will recompile the Engine at least 1 time for each type of build you want to produce. If you want a Development build, then MSVS will build the binaries for that type. If you want a Shipping build, then MSVS will build the binaries for that type as well. Each type requires a full build at least 1 time. People who whine &quot;I changed 1 little thing and Engine is recompiling in full again&quot; don't know what they're doing or maybe are not aware of this aspect.<br />
<br />
2) Always use the &quot;Build&quot; option in MSVS (not &quot;Rebuild&quot;). MSVS will do incremental builds that way and recompile only what's affected by your changes in the source code.<br />
<br />
On my PC it took over 1-2 hours to build everything. While at it, before the build process, I've set to 1 the ALLOW_CONSOLE and ALLOW_CONSOLE_IN_SHIPPING defines in <i>Build.h</i>. Why? Cuz we want the console available to use in our Shipping build of the game. BY DEFAULT ALL SHIPPING BUILDS HAVE THE CONSOLE CREATION CODE COMMENTED OUT:<br />
<br />
<pre>
<code>ULocalPlayer* UGameViewportClient::SetupInitialLocalPlayer(FString&amp; OutError)
{
..
#if ALLOW_CONSOLE
	// Create the viewport's console.
	ViewportConsole = NewObject&lt;UConsole&gt;(this, GetOuterUEngine()-&gt;ConsoleClass);
	// register console to get all log messages
	GLog-&gt;AddOutputDevice(ViewportConsole);
#endif // !UE_BUILD_SHIPPING
..
}</code></pre>
</div>

That's why you &quot;can't enable the console&quot;. That doesn't mean we can't create it artificially <img src="https://www.unknowncheats.me/forum/images/smilies/wink.gif" border="0" alt="" title="Wink" class="inlineimg" /> We can.<br />
<br />
So for those who are lazy f*cks, here's the compiled project with debug symbols and all. You can also play the game:<br />
<br />
<a rel="nofollow" href="https://mega.nz/file/CAxVzCJR#5cqWuShBm3Dc_hNAfrwi_M9lFT337OvZWsOXKRbhkW4" target="_blank">Download ShooterGame 4.26.2 with debug symbols (642MB)</a>.<br />
<br />
The game executable you want to fiddle with is in .\WindowsNoEditor\ShooterGame\Binaries\Win64 path:<br />
<br />
<ul><li>ShooterGame-Win64-Shipping.exe</li>
<li>ShooterGame-Win64-Shipping.pdb</li>
</ul><br />
Why go through all of this? Simple: ToF uses the same Engine build as the game that I just compiled. That means the CORE functions are going to be looking the same, more or less, and their ASM architecture will match 99.99%. That being said, you can copy a sequence of bytes from the prologue or body of a function in ShooterGame and scan for it in QRSL and you will find it. Given I was a good boy and also shipped in the .pdb file, you will now also know the NAME of the function.<br />
<br />
What does the above solve: &quot;I think the function is called..&quot;, &quot;how or where can I find ProcessEvent?&quot;, etc. If not even with this kind of help you won't make it, then I'm at a loss for words.<br />
<br />
-- <br />
<br />
<b>EXAMPLE OF CROSS-FINDING FUNCTIONS</b><br />
<br />
Example (top is ShooterGame, bottom is QRSL):<br />
<br />
<img src="https://i.imgur.com/TRNgcc4.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Oh, gee, where or how can I find <b>ProcessEvent</b>?:<br />
<br />
<img src="https://i.imgur.com/UcXsaNa.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Right-click it, Follow in Disassemble, select several bytes and Shift+C to copy them:<br />
<br />
<img src="https://i.imgur.com/7FKK3No.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Go to QRSL, press Ctrl+B, paste bytes, tick Entire Block:<br />
<br />
<img src="https://i.imgur.com/4TKD7FM.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
And voila, 1 result:<br />
<br />
<img src="https://i.imgur.com/QnksNgK.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
<img src="https://i.imgur.com/Y1LmX8Y.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
--<br />
<br />
<b>KNOWLEDGE IS POWER</b><br />
<br />
With the logic presented above plus many other additional tricks and static analysis, I was able to understand how UE4 works, why people dump Objects and Names, what dumping of that relies on, how it is done, devise a patch to hook almost ANY UE4 version so that I can get very fast to UObjects of interest with just a simple CE table.<br />
<br />
So below, here's my table for this game with several critical CORE stuff that you will most definitely make use of. Note that NOT EVERYTHING is updated in there, as it's a work in progress. My aim isn't to play or hack ToF, but to expose as much as possible, so any with a little bit of brain can work their way out:<br />
<br />
<img src="https://i.imgur.com/7XnKieI.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
<a rel="nofollow" href="https://mega.nz/file/iUxkSBhA#oFec-wYNQV0oG4ZCYuHqOJ2cnpVJM00EuZK-jhBQl4I" target="_blank">Download table</a><br />
<br />
NOTE: PLEASE DO NOT YET UPLOAD IT TO UC! SOME SCRIPTS ARE NOT UPDATED. Will submit it myself once I'm done with it.<br />
<br />
Why is the table useful at this point in time? Let's open the <b>[ Initialize ]</b> script:<br />

<ul><li>You have a way to run your own <b>staticFindObject</b>:<br />
<br />
<pre>
<code>
  function staticFindObjectEx( sa, sb, sc )
  local StaticFindObject = getAddressSafe( &quot;StaticFindObject&quot; )
  if StaticFindObject == 0x0 then return 0 end
  local s, Class, InOuter, UObject = allocateMemory( 256 ), 0, 0, 0
  if sb == nil and sc == nil then
    writeString( s, sa, true )
    writeBytes( s + #sa * 2, 0 )
    UObject = executeCodeEx( 0, nil, StaticFindObject, 0, -1, s, 1 )
  elseif sc == nil then
    writeString( s, sa, true )
    writeBytes( s + #sa * 2, 0 )
    Class = executeCodeEx( 0, nil, StaticFindObject, 0, -1, s, 1 )
    writeString( s, sb, true )
    writeBytes( s + #sb * 2, 0 )
    UObject = executeCodeEx( 0, nil, StaticFindObject, Class, -1, s, 0 )
  else
    writeString( s, sa, true )
    writeBytes( s + #sa * 2, 0 )
    Class = executeCodeEx( 0, nil, StaticFindObject, 0, -1, s, 1 )
    writeString( s, sb, true )
    writeBytes( s + #sb * 2, 0 )
    InOuter = executeCodeEx( 0, nil, StaticFindObject, 0, -1, s, 1 )
    writeString( s, sc, true )
    writeBytes( s + #sc * 2, 0 )
    UObject = executeCodeEx( 0, nil, StaticFindObject, Class, InOuter, s, 0 )
  end
  deAlloc( s )
  return UObject
end

local t = staticFindObjectEx( &quot;ObjectProperty&quot;, &quot;Engine.Engine&quot;, &quot;GameViewport&quot; )
if t &lt; 1 then stopExec( &quot;'GameViewport' ObjectProperty not found.&quot; ) end
</code>
</pre>

<li> There's one single hook you need to get the UObject relationship starting from <b>LocalPlayer</b>:<br />
<br />
<pre>
<code>
local _script = [[

  define( Trampoline__01, Trampolines+00 )
  define( Trampoline__02, Trampolines+10 )

]]..[[[ENABLE]

  alloc( MyHooks, 0x1000 )
  registersymbol( MyHooks )

  label( FViewport__Draw_hook )
  registersymbol( FViewport__Draw_hook )

  label( FViewport__Draw_hookspot_orig )
  registersymbol( FViewport__Draw_hookspot_orig )

  label( LocalPlayer )
  registersymbol( LocalPlayer )
  label( GameViewportClient )
  registersymbol( GameViewportClient )
  label( Console )
  registersymbol( Console )
  label( PlayerController )
  registersymbol( PlayerController )
  label( CheatManager )
  registersymbol( CheatManager )
  label( PlayerCharacter )
  registersymbol( PlayerCharacter )

..
..

  MyHooks:
  db CC

  align 10 CC

  FViewport__Draw_hook:
  mov [LocalPlayer],rax
  mov rcx,[rax+70]
  mov [GameViewportClient],rcx
  mov rcx,[rcx+40]
  mov [Console],rcx
  mov rcx,[rax+30]
  test rcx,rcx
  je short @f
    mov [PlayerController],rcx
    mov rdx,[rcx+338]
    mov [CheatManager],rdx
    mov rcx,[rcx+250]
    test rcx,rcx
    je short @f
      mov [PlayerCharacter],rcx
  @@:
  FViewport__Draw_hookspot_orig:
  readmem( FViewport__Draw_hookspot, 7 )
  jmp far FViewport__Draw_hookspot+7
  </code>
  </pre>
  
I scan for and hook the return from <i>GetGamePlayers</i> function. This aob works/worked on 90% of my UE4 games...
<br />

Code:
<pre>
<code>
-- hook the return from GetGamePlayers
local aob_in_FViewport_Draw = &quot;488B40??4885C074??F680????????02&quot;
sl = aobScanEx( aob_in_FViewport_Draw )
if not sl or sl.Count &lt; 1 then stopExec( &quot;'aob_in_FViewport_Draw' not found.&quot; ) end
t = tonumber( sl[0], 16 )
unregisterSymbol( &quot;FViewport__Draw_hookspot&quot; )
registerSymbol( &quot;FViewport__Draw_hookspot&quot;, t, true )
</code>
</pre>

<li> Then there's a sh!t ton of aobs that scan for critical UE4 stuff:<br />
<br />
<pre><code>unregistersymbol( StaticFindObject )
  unregistersymbol( FConsoleManager__ProcessUserConsoleInput_hookspot )
  unregistersymbol( Static__Exec_patchspot )
  unregistersymbol( UObject__CallFunctionByNameWithArguments_hookspot )
  unregistersymbol( UPlayer__Exec_epilogue )
  unregistersymbol( UPlayer__Exec_hookspot )
  unregistersymbol( UPlayer__Exec_prologue )
  unregistersymbol( AActor__Tick )
  unregistersymbol( GameEngine__ViewportClient_offset )
  unregistersymbol( UConsole__StaticClass )
  unregistersymbol( FOutputDeviceRedirector__AddOutputDevice )
  unregistersymbol( GetGlobalLogSingleton )
  unregistersymbol( StaticConstructObject_Internal )
  unregistersymbol( FStaticConstructObjectParameters__FStaticConstructObjectParameters )
  unregistersymbol( FObjectInitializer__AssertIfInConstructor )
  unregistersymbol( pszNewObjectString )
  unregistersymbol( GEngine )
  unregistersymbol( pszEngineString )
  unregistersymbol( FViewport__Draw_hookspot )</code></pre>

<p>You can find the aobs laid out for you, waiting to be reused in other projects :P</p><br />
<br /></li>

<li> There's an incipient hierarchy tree based on the hook I was mentioning above in the [ Debug ] section:<br />
<br />
<img src="https://i.imgur.com/tEe1oh3.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br /></li>
<li> There is a script that enables the use of the SET command in the console, with the help of which you can manually <b>set</b> UProperty-es :P (e.g.: <i>set Engine.Actor bCanBeDamaged True</i>)<br />
<br /></li>
<li> There is a script that helps with hooking <i>UPlayer::Exec</i> so that when you pass it an UObject as context and run said function in the console, it will automatically be made executable (UFUNC_Exec) and allow the running of that command in the Class context of that object:<br />
<br />
<img src="https://i.imgur.com/rBIDN6T.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Pretty much like what this user here is instructing, conceptually: <a href="https://www.unknowncheats.me/forum/tower-of-fantasy/513961-pvp-bots-cheat-engine-calling-game-functions.html" target="_blank">PVP bots in Cheat Engine (calling game functions)</a>.<br />
<br /></li>
<li> Know some CVars? You can unrestrict them :P<br />
<br /></li>
<li> Lastly, if you're tired of seeing the smear of text in the Console, you can clear it out (I'm not 100% sure the offset is correct in that script; will need to check).</li>
</ul>
<br />
--<br />
<br />
<b>Cake-san</b><br />
<br />
If you want a real time method to faster determine which UObject is at which offset in some other UObject, then this guy really nailed it:<br />
<br />
<a rel="nofollow" href="https://fearlessrevolution.com/viewtopic.php?p=167452#p167452" target="_blank">Get Cake-san's awesome Basic UE4 Win64 Base Table</a><br />
<br />
Make sure you download <font color="red">&quot;Automate version Update 7.3&quot;</font> script. Not others. Why? There's been several fixes put into update, so it makes no sense to use older versions.<br />
<br />
So.. let's say we use both my table and his in parallel:<br />
<br />
<i>[My table]</i>:<br />
<br />
&lt; before everything, make sure you can actually access the game's memory space; won't talk about bypassing here, now &gt;<br />
<br />
1) Open table and run [ Initialize ] script.<br />
2) Considering you are in-game, expand [ Debug ] section and LocalPlayer node. Take note of what you see there:<br />
<br />
<img src="https://i.imgur.com/X7ubn7T.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Note that your address will be different. It's normal. Just remember YOUR value from the red rectangle.<br />
<br />
[i]Cake-san's table[/b]:<br />
<br />
1) Open table in another CE instance.<br />
2) Select QRSL.exe game process.<br />
3) Activate &quot;Unreal Engine&quot; script and wait for it to finish its job. You'll know when it's done processing, as the Lua Engine window will close and sub-scripts expand. Yes, it will take about 2-3 minutes. This is what you'll see:<br />
<br />
<img src="https://i.imgur.com/VXfFA2v.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
4) Activate &quot;Enable UE Structure Lookup&quot; script.<br />
5) Click Memory View, then &quot;Tools&quot; from the top menu, then &quot;Dissect data/structures&quot;, so this will open up:<br />
<br />
<img src="https://i.imgur.com/ENjOD6f.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
6) Remember that address from the red rectangle in my table? Switch to the other CE instance, double-click and copy it (or just move the window aside so you can see it). Back to Cake-san's table, paste it in the top input field:<br />
<br />
<img src="https://i.imgur.com/eHBUTYg.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
7) Now click &quot;Structures&quot; from the top menu and &quot;Define new structure&quot; and you will see these:<br />
<br />
<img src="https://i.imgur.com/328EdwC.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
<img src="https://i.imgur.com/tB7YGYN.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
So.. where's <b>PlayerController</b> in <b>LocalPlayer</b>, I hear?? Well, it's at offset 0x30. See.. not everything revolves around CheatGear and SDK dumping <img src="https://www.unknowncheats.me/forum/images/smilies/smile.gif" border="0" alt="" title="Big Grin" class="inlineimg" /><br />
<br />
Speaking of dumping..<br />
<br />
8) Run this script, then check your Desktop (although the file should already open for you in Notepad):<br />
<br />
<img src="https://i.imgur.com/CK48IZm.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
<img src="https://i.imgur.com/UmvJ1Gs.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
There you have it, enjoy! <img src="https://www.unknowncheats.me/forum/images/smilies/smile.gif" border="0" alt="" title="Big Grin" class="inlineimg" /><br />
<br />
--<br />
<br />
<b>WARNING AHEAD</b><br />
<br />
PROXIMA will take measures and enhance their anti-cheat so CE can't be used at all in the future. This has been the evolution of all MP anti-cheats and developers across time <img src="https://www.unknowncheats.me/forum/images/smilies/smile.gif" border="0" alt="" title="Big Grin" class="inlineimg" /> It will happen again. So please appreciate the learning path and having been taught useful things in this topic rather than lash out with &quot;it's your fault PROXIMA patched everything&quot;, mkay? Cheers!<br />
<br />
BR,<br />
Sun
