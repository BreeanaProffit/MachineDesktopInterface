# The Machine Desktop Interface (MDI)

A tool to turn ML outputs into meaningful actions within windowed desktop applications on Windows or Mac machines.

Open the application you want to interact with and, if possible, place it in windowed mode. The smaller form will speed up MSS code (which supplies screen data as input, for example, to a neural network).

### EXAMPLE:

> mdi = MachineDesktopInterface('my application name', ['w', 'a', 's', 'd'])  
> while True:
> &nbsp;&nbsp;&nbsp;&nbsp; \#get a generator that will provide screen captures of the application -- this also initializes and resizes the application to 800x600    
> &nbsp;&nbsp;&nbsp;&nbsp; screenOutput = getScreenOutputModel(800, 600)  
> &nbsp;&nbsp;&nbsp;&nbsp; screenCapture = next(screenOutput)  
> &nbsp;&nbsp;&nbsp;&nbsp; \#input screen capture's numpy data as input to a neural network, get 4 outputs  
> &nbsp;&nbsp;&nbsp;&nbsp; \#output = [0.2, 0.3, 0.9, 0.3]  
> &nbsp;&nbsp;&nbsp;&nbsp; \#transform your list into a list of booleans and floats according to the type of input  
> &nbsp;&nbsp;&nbsp;&nbsp; \#interfaceInput = [False, False, True, False]  
> &nbsp;&nbsp;&nbsp;&nbsp; mdi.interpretAction(interfaceInput)  

## INPUTS: ACTIONS

##### Possible Observed Keys and Their Expected Values

When you initialize the Machine Desktop Interface you must also pass in a list of observed keys. These are the defined set of keys which the MDI can interact with, and the list plays an inportant role in that it defines the expected order that the actions list (passed into the interpretAction method) must follow. In the above example, the observed keys are 'w', 'a', 's', and 'd' in that order. So when we pass [False, False, True, False] in to interpretAction, the interpreter will read it as: 'w' is not being pressed, 'a' is not being pressed, 's' **is** being pressed, and 'd' is not being pressed.  
  
The [False, False, True, False] object is a list of *action values*, and the set of actions that are actively being applied by the MDI to your application will be updated each time you call the interpretAction method and supply a similar list of action values. Each observed key expects a specific kind of action value to be supplied. See below for details:

##### Symbols
* '\t', '\n', '\r', ' ', '!', '"', '#', '$', '%', '&', "'", '(', ')', '*', '+', ',', '-', '.', '/', ':', ';', '<', '=', '>', '?', '@', '[', '\\', ']', '^', '_', '`', '{', '|', '}', '~'
##### Alphanumeric
* '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z'
##### Special Keys
* 'accept', 'add', 'alt', 'altleft', 'altright', 'apps', 'backspace', 'browserback', 'browserfavorites', 'browserforward', 'browserhome', 'browserrefresh', 'browsersearch', 'browserstop', 'capslock', 'clear', 'convert', 'ctrl', 'ctrlleft', 'ctrlright', 'decimal', 'del', 'delete', 'divide', 'down', 'end', 'enter', 'esc', 'escape', 'execute', 'f1', 'f10', 'f11', 'f12', 'f13', 'f14', 'f15', 'f16', 'f17', 'f18', 'f19', 'f2', 'f20', 'f21', 'f22', 'f23', 'f24', 'f3', 'f4', 'f5', 'f6', 'f7', 'f8', 'f9', 'final', 'fn', 'hanguel', 'hangul', 'hanja', 'help', 'home', 'insert', 'junja', 'kana', 'kanji', 'launchapp1', 'launchapp2', 'launchmail', 'launchmediaselect', 'left', 'modechange', 'multiply', 'nexttrack', 'nonconvert', 'num0', 'num1', 'num2','num3', 'num4', 'num5', 'num6', 'num7', 'num8', 'num9', 'numlock', 'pagedown', 'pageup', 'pause', 'pgdn', 'pgup', 'playpause', 'prevtrack', 'print', 'printscreen', 'prntscrn', 'prtsc', 'prtscr', 'return', 'right', 'scrolllock', 'select', 'separator', 'shift', 'shiftleft', 'shiftright', 'sleep', 'space', 'stop', 'subtract', 'tab', 'up', 'volumedown', 'volumemute', 'volumeup', 'win', 'winleft', 'winright', 'yen', 'command', 'option', 'optionleft', 'optionright'
##### Mouse Options
* 'primary', 'middle', 'secondary', 'Should_Move_Mouse', 'Move_To_Mouse_X', 'Move_To_Mouse_Y', 'Mouse_Offset_X', 'Mouse_Offset_Y'  
  
##### Notes about the keys  
For most of the keys listed above the functionality as described in the ['w', 'a', 's', 'd'] example applies. You can pass every one of these keys in any order into the observed keys list during initialization, and for each of the categories except for mouse options you can simply pass along a boolean as an action value.  
  
##### Notes about the mouse options
Mouse options are more complicated. The 'primary', 'middle', and 'secondary' keys represent the buttons common to a mouse, and they expect boolean action values. Setting the action value to true for one of these will trigger a click action.  
  
'Should_Move_Mouse' also expects a boolean action value, and enabling it allows the mouse to change position according to 'Move_To_Mouse_X', 'Move_To_Mouse_Y', 'Mouse_Offset_X', and 'Mouse_Offset_Y'. To be clear, if mouse movement is enabled then it will always occur before a click.  
  
'Move_To_Mouse_X', 'Move_To_Mouse_Y', 'Mouse_Offset_X', and 'Mouse_Offset_Y' expect float values between 0.0 and 1.0. 1.0 represents the full width or height of the application.  
  
The Move_To keys simply place the mouse -- for example 'Move_To_Mouse_X' with an action value of 0.6 will move the mouse to a point that is 60% of the width from the left of the application.  
  
Likewise, the Mouse_Offset keys take the current position and apply an offset. If the mouse is at x = 0.6 already and 'Mouse_Offset_X' is supplied with an action value of 0.1, then the mouse will move to 0.7 or 70% of the width from the left of the application.

## OUTPUTS: SCREEN CAPTURES

##### Getting feedback after taking actions
Once you take actions with the MDI, the state of the application you are working with should change. For example, in a game you might expect to see a character move. As a gamer you would implicitly take that as feedback that pressing a certain button moved your character. For many reinforcement learning applications, that same feedback is required in order for the machine to learn how to play. The Machine Desktop Interface returns a generator in the getScreenOutputModel method, which you can use to generate numpy arrays representing one frame of the application.  
  
Note that the machine will see what you see -- if another application blocks your application, that will be captured. If the window of your application is no longer visible, or is minimized, subsequent calls to the generator will yield no results. And in fact, minimizing an application is an easy way to cancel actions from the MDI.
