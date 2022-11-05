# Этой игре полный пиздец (учебники и знания внутри)
### p.s. речь идет о ТоФе
жеска напереводил целую кучу перевода с помощью deepl

<a rel="nofollow" href="https://www.unknowncheats.me/forum/tower-of-fantasy/514006-game-totally-broken-bit-tutorials-knowledge-inside.html" target="_blank">Оригинал от SunBeam тут </a>
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
	
[Перевод картинки сверху]
<br />
ВНИМАНИЕ<br />
Прежде чем вы сможете получить доступ к репозиторию на сайте https://github.com/EpicGames/UnrealEngine, вы должны: 
1) быть подписчиком Unreal Engine. 
2) иметь учетную запись на GitHub. 
3) связать свою учетную запись GitHub с учетной записью Unreal Engine, как описано на странице UE4 на GitHub.
<br />
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
<b>ПРИМЕР CROSS_FINDING ФУНКЦИЙ</b><br />
<br />
Пример (вверху - ShooterGame, внизу - QRSL):<br />
<br />
<img src="https://i.imgur.com/TRNgcc4.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
О, нееет, где или как я могу найти <b>ProcessEvent</b>?<br />
<br />
<img src="https://i.imgur.com/UcXsaNa.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Щелкните правой кнопкой мыши, выполните команду Disassemble, выберите несколько байтов и Shift+C для их копирования:<br />
<br />
<img src="https://i.imgur.com/7FKK3No.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Перейдите в QRSL, нажмите Ctrl+B, вставьте байты, поставьте галочку на Entire Block:<br />
<br />
<img src="https://i.imgur.com/4TKD7FM.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
И вуаля, 1 результат:<br />
<br />
<img src="https://i.imgur.com/QnksNgK.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
<img src="https://i.imgur.com/Y1LmX8Y.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
--<br />
<br />
<b>ЗНАНИЕ - СИЛА</b><br />
<br />
С помощью представленной выше логики плюс многих других дополнительных приемов и статического анализа я смог понять, как работает UE4, почему люди сбрасывают объекты и имена, на что опирается сброс, как это делается, разработать патч для подключения практически любой версии UE4, чтобы я мог очень быстро добраться до интересующих меня UObjects с помощью простой таблицы CE.<br />
<br />
Итак, ниже приведена моя таблица для этой игры с несколькими важнейшими CORE вещами, которые вам обязательно пригодятся. Обратите внимание, что НЕ ВСЁ здесь обновлено, так как это работа в процессе. Моя цель не в том, чтобы играть или взламывать ToF, а в том, чтобы раскрыть как можно больше, чтобы любой, у кого есть хоть немного мозгов, мог разобраться:<br />
<br />
<img src="https://i.imgur.com/7XnKieI.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
<a rel="nofollow" href="https://mega.nz/file/iUxkSBhA#oFec-wYNQV0oG4ZCYuHqOJ2cnpVJM00EuZK-jhBQl4I" target="_blank">Download table</a><br />
<br />
ВНИМАНИЕ: ПОЖАЛУЙСТА, ПОКА НЕ ЗАГРУЖАЙТЕ ЕГО В UC! НЕКОТОРЫЕ СКРИПТЫ НЕ ОБНОВЛЕНЫ. Я отправлю его сам, как только закончу работу над ним.<br />
<br />
Почему таблица полезна в данный момент? Давайте откроем <b>[ Initialize ]</b> скрипт:<br />

<ul><li>У вас есть возможность управлять своим <b>staticFindObject</b>:<br />
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

<li> Есть один единственный хук, который нужен для получения отношения UObject, начиная с <b>LocalPlayer</b>:<br />
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
  
Я сканирую и подключаю возврат из функции <i>GetGamePlayers</i>. Этот aob работает/работал на 90% моих игр UE4...
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

<li> Затем есть тонна aob'ов, которые сканируют критические материалы UE4:<br />
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

Вы можете найти aobs, разложенные для вас, ожидающие повторного использования в других проектах :P<br />
<br /></li>

<li> В разделе [ Debug ] есть зарождающееся иерархическое дерево, основанное на крючке, о котором я говорил выше:<br />
<br />
<img src="https://i.imgur.com/tEe1oh3.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br /></li>
<li> Существует сценарий, который позволяет использовать команду SET в консоли, с помощью которого вы можете вручную <b>установить</b> UProperty-es :P (например: <i>set Engine.Actor bCanBeDamaged True</i>)<br />
<br /></li>
<li> Существует сценарий, который помогает подключить <i>UPlayer::Exec</i> таким образом, что когда вы передаете ему UObject в качестве контекста и запускаете указанную функцию в консоли, она автоматически становится исполняемой (UFUNC_Exec) и позволяет выполнить эту команду в контексте класса этого объекта.:<br />
<br />
<img src="https://i.imgur.com/rBIDN6T.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Очень похоже на то, чему учит этот пользователь, концептуально: <a href="https://www.unknowncheats.me/forum/tower-of-fantasy/513961-pvp-bots-cheat-engine-calling-game-functions.html" target="_blank">PVP bots in Cheat Engine (calling game functions)</a>.<br />
<br /></li>
<li> Знаете некоторых CVARов? Вы можете снять с них ограничения :P<br />
<br /></li>
<li> Наконец, если вам надоело видеть размазанный текст в консоли, вы можете очистить его (я не уверен на 100%, что смещение в этом скрипте правильное; нужно будет проверить).</li>
</ul>
<br />
--<br />
<br />
<b>Cake-san</b><br />
<br />
Если вам нужен метод реального времени для более быстрого определения того, какой UObject находится по смещению в каком-то другом UObject, то этот парень действительно сделал это:<br />
<br />
<a rel="nofollow" href="https://fearlessrevolution.com/viewtopic.php?p=167452#p167452" target="_blank">Get Cake-san's awesome Basic UE4 Win64 Base Table</a><br />
<br />
Убедитесь, что вы загрузили <font color="red">&quot;Automate version Update 7.3&quot;</font> скрипт. Не другие. Почему? В обновление было внесено несколько исправлений, поэтому нет смысла использовать старые версии.<br />
<br />
Итак... допустим, мы используем мой и его стол параллельно:<br />
<br />
<i>[My table]</i>:<br />
<br />
&lt; прежде всего, убедитесь, что вы действительно можете получить доступ к пространству памяти игры; не будем говорить об обходе здесь и сейчас &gt;<br />
<br />
1) Откройте таблицу и запустите скрипт [ Initialize ].<br />
2) Учитывая, что вы находитесь в игре, разверните раздел [ Debug ] и узел LocalPlayer. Обратите внимание на то, что вы там видите:<br />
<br />
<img src="https://i.imgur.com/X7ubn7T.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Обратите внимание, что ваш адрес будет другим. Это нормально. Просто запомните ВАШЕ значение из красного прямоугольника.<br />
<br />
[i]Cake-san's table[/b]:<br />
<br />
1) Откройте таблицу в другом экземпляре CE.<br />
2) Выберите игровой процесс QRSL.exe.<br />
3) Активируйте скрипт &quot;Unreal Engine&quot; и подождите, пока он закончит свою работу. Вы узнаете, когда он закончит обработку, так как окно Lua Engine закроется, а подскрипты развернутся. Да, это займет около 2-3 минут. Вот что вы увидите:<br />
<br />
<img src="https://i.imgur.com/VXfFA2v.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
4) Активируйте скрипт &quot;Enable UE Structure Lookup&quot;.<br />
5) Нажмите Memory View, затем &quot;Tools&quot; из верхнего меню, затем &quot;Dissect data/structures&quot;, откроется вот это:<br />
<br />
<img src="https://i.imgur.com/ENjOD6f.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
6) Помните адрес из красного прямоугольника в моей таблице? Переключитесь на другой экземпляр CE, дважды щелкните и скопируйте его (или просто отодвиньте окно, чтобы его было видно). Вернитесь к таблице господина Торта и вставьте его в верхнее поле ввода:<br />
<br />
<img src="https://i.imgur.com/eHBUTYg.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
7) Теперь нажмите &quot;Структуры&quot; в верхнем меню и &quot;Определить новую структуру&quot;, и вы увидите следующее:<br />
<br />
<img src="https://i.imgur.com/328EdwC.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
<img src="https://i.imgur.com/tB7YGYN.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Итак... где же <b>PlayerController</b>, я слышал в <b>LocalPlayer</b>?? Ну, он находится по смещению 0x30. Видите... не все крутится вокруг CheatGear и SDK-дампинга <img src="https://www.unknowncheats.me/forum/images/smilies/smile.gif" border="0" alt="" title="Big Grin" class="inlineimg" /><br />
<br />
Кстати, о дампинге...<br />
<br />
8) Запустите этот скрипт, затем проверьте свой Рабочий стол (хотя файл уже должен быть открыт для вас в Блокноте):<br />
<br />
<img src="https://i.imgur.com/CK48IZm.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
<img src="https://i.imgur.com/UmvJ1Gs.png" border="0" alt="" onload="NcodeImageResizer.createOn(this);" /><br />
<br />
Вот и все, наслаждайтесь! <img src="https://www.unknowncheats.me/forum/images/smilies/smile.gif" border="0" alt="" title="Big Grin" class="inlineimg" /><br />
<br />
--<br />
<br />
<b>ПРЕДОСТЕРЕЖЕНИЕ</b><br />
<br />
PROXIMA примет меры и усилит анти-чит, чтобы в будущем CE вообще нельзя было использовать. Такова эволюция всех анти-читов и разработчиков MP во все времена <img src="https://www.unknowncheats.me/forum/images/smilies/smile.gif" border="0" alt="" title="Big Grin" class="inlineimg" /> Это случится снова. Так что, пожалуйста, оцените путь обучения и то, что вас научили полезным вещам в этой теме, а не бросайтесь словами &quot;это ваша вина, что PROXIMA все исправила&quot;, хорошо? Будьте здоровы!<br />
<br />
С наилучшими пожеланиями,<br />
Sun</div>
<br />
<br />
<br />
<img src="./nasral.gif" border="0" alt="" onload="NcodeImageResizer.createOn(this);" />
урааа конес.
