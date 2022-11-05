# This game is going to be totally broken in a bit (tutorials and knowledge inside)
### переведено с помощью DeepL
<hr>
<div>Во-первых, кажется, что некоторым людям трудно &quot;читать&quot; или &quot;говорить&quot; на языке UE4. Не говоря уже о том, как плохо они передают эту информацию общественности. До этой игры я многое узнал о UE4 тяжелым и утомительным способом: скачать исходный код, скомпилировать редактор, скомпилировать устаревшую игру (ShooterGame) с отладочными символами и возиться. Так я узнал о внутреннем фреймворке, о том, как переплетаются объекты, и еще много чего из публичного исходного кода. Так что те из вас, кто думает, что взлом игр UE4 сводится к сбросу объектов и SDK... знайте, что дело не в этом. Это всего лишь последняя часть, до которой вы доберетесь после довольно интересного пути обучения. Так что нет, все дело в изучении и знакомстве с внутренней структурой Unreal Engine. Как только вы освоите ее, вы сможете приступить к работе. Почему? Потому что любая другая игра будет вращаться вокруг тех же принципов.<br />
<br />
Приготовьтесь, это будет большой пост.<br />
<br />
--<br />
<br />
<b>КАК СОБРАТЬ ДВИЖОК</b> (для локального тестирования, обучения и т.п.)<br />
<br />
Вы можете получить исходный код на github компании Epic, после <b><u>регистрации(signing up)</u></b> и ознакомления с их правилами ниже:<br />
<br />
<img src="https://i.imgur.com/miZi7h4.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Загрузите <a rel="nofollow" href="https://github.com/EpicGames/UnrealEngine/releases/tag/4.26.2-release" target="_blank">4.26.2-release</a>. Инструкции по созданию редактора на MSVS2017 находятся <a rel="nofollow" href="https://github.com/EpicGames/UnrealEngine/tree/4.26" target="_blank">здесь</a>. (раздел &quot;Getting up and running&quot;). Шаг 1 можно пропустить, так как вы уже скачали исходный код. И шаг 2, если вы уже установили MSVS2017. Ваша цель - шаги 3 - 6.<br />
<br />
Почему 4.26? Потому что это то, что использует ToF:<br />
<br />
<img src="https://i.imgur.com/IG5Kre8.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Затем вы хотите получить данные для DEMO GAME, которые вы можете удобно найти в Epic Games Launcher, в разделе <b>Vault</b>:<br />
<br />
<img src="https://i.imgur.com/mJpiFif.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Я лично предпочитаю и использую проект <b>ShooterGame</b>.<br />
<br />
На что следует обратить внимание:<br />
<br />
1) MSVS перекомпилирует движок по крайней мере 1 раз для каждого типа сборки, которую вы хотите создать. Если вы хотите сборку для разработки, то MSVS будет собирать двоичные файлы для этого типа. Если вы хотите сборку Shipping, то MSVS создаст двоичные файлы и для этого типа. Каждый тип требует полной сборки по крайней мере 1 раз. Люди, которые ноют &quot;Я изменил одну мелочь, и Engine снова полностью перекомпилируется&quot;, не знают, что они делают, или, возможно, не осведомлены об этом аспекте.<br />
<br />
2) Всегда используйте опцию &quot;Build&quot; в MSVS (не &quot;Rebuild&quot;). Таким образом MSVS будет делать инкрементные сборки и перекомпилировать только то, на что влияют ваши изменения в исходном коде.<br />
<br />
На моем ПК на сборку всего ушло более 1-2 часов. При этом перед процессом сборки я установил значение 1 для определений ALLOW_CONSOLE и ALLOW_CONSOLE_IN_SHIPPING в <i>Build.h</i>. Почему? Потому что мы хотим, чтобы консоль была доступна для использования в нашей сборке игры для доставки.  ПО УМОЛЧАНИЮ ВО ВСЕХ ПОСТАВЛЯЕМЫХ СБОРКАХ КОД СОЗДАНИЯ КОНСОЛИ ЗАКОММЕНТИРОВАН:<br />
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

Вот почему вы &quot;не можете включить консоль&quot;. Но это не значит, что мы не можем создать ее искусственно. <img src="https://www.unknowncheats.me/forum/images/smilies/wink.gif" border="0" alt="" title="Wink" class="inlineimg" /> Мы можем.<br />
<br />
Итак, для тех, кто ленится, вот скомпилированный проект с отладочными символами и всем остальным. Вы также можете поиграть в игру:<br />
<br />
<a rel="nofollow" href="https://mega.nz/file/CAxVzCJR#5cqWuShBm3Dc_hNAfrwi_M9lFT337OvZWsOXKRbhkW4" target="_blank">Download ShooterGame 4.26.2 with debug symbols (642MB)</a>.<br />
<br />
Исполняемый файл игры, с которым вы хотите поработать, находится по пути .\WindowsNoEditor\ShooterGame\Binaries\Win64:<br />
<br />
<ul><li>ShooterGame-Win64-Shipping.exe</li>
<li>ShooterGame-Win64-Shipping.pdb</li>
</ul><br />
Зачем проходить через все это? Все просто: ToF использует ту же сборку Engine, что и игра, которую я только что скомпилировал. Это означает, что функции CORE будут выглядеть одинаково, более или менее, и их архитектура ASM будет совпадать на 99,99%. Учитывая это, вы можете скопировать последовательность байтов из пролога или тела функции в ShooterGame и просканировать ее в QRSL, и вы ее найдете. Учитывая, что я был хорошим мальчиком и также отправил в .pdb файл, вы теперь также будете знать НАЗВАНИЕ функции.<br />
<br />
Что решает вышеперечисленное: &quot;кажется, функция называется...&quot;, &quot;как или где я могу найти ProcessEvent?&quot; и т.д. Если даже с такой помощью у вас ничего не получится, то я в растерянности.<br />
<br />
-- <br />
<br />
<b>ПРИМЕР смINDING FUNCTIONS</b><br />
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
Sun</div>
