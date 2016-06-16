![ScreenShot.png](http://www.stresstimulus.com/image/CustomMessageBox.png)
<h2>Introduction</h2>

When I do .NET WinForms programming, I often need a message box with 
different options. .NET offers the <code>MessageBox.Show()</code> API which 
shows a standard Windows message box. The Windows message box has very limited 
button combinations (like OK-Cancel, Yes-No, and a few more). I needed a message 
box with customizable button captions (like Install-Don't Install or Send Error 
Report-Don't Send). Also, it would be nice to have a "Don't show this message 
again" checkbox. So I developed a message box Form (derived from <code>Form</code>) 
which would allow me all that and I thought I'd share it. This message box Form was used in the development of <a href="http://www.stresstimulus.com">StresStimulus</a>, a performance testing tool.
<h2>Features</h2>
<ul>
	<li>1-3 buttons with custom captions.</li>
	<li>Optional checkbox with custom text.</li>
	<li>Optional icon. Either a standard Windows icon or any custom icon.</li>
	<li>Custom dimensions of the message box.</li>
</ul>
<h2>Constructors</h2>
The message box comes with three constructors. The simplest one takes the 
message box text and title. The message can be any length and width because the 
message box stretches to fit the text. To show the message box, call the <code>
ShowDialog(this)</code> method.<br>

<pre>MsgBox db = new MsgBox("This is a test message", "This is a test title");
db.ShowDialog(this); //show the message box.</pre>
To add an icon to the top 
left corner, use one of the two other constructors. You can either provide a 
standard Windows icon using the <code>System.Windows.Forms.MessageBoxIcon</code> enumeration 
or any custom icon object. You can also get a standard Windows icon object 
through the <code>System.Drawing.SystemIcons</code> class.<br><br>
<pre>//Using the MessageBoxIcon enum.
MsgBox db = new MsgBox("This is a test message", 
            "This is a test title", MessageBoxIcon.Information);
db.ShowDialog(this); //show the message box.

//Using the System.Drawing.SystemIcons class.
MsgBox db = new MsgBox("This is a test message", 
            "This is a test title", SystemIcons.Information);
db.ShowDialog(this); //show the message box.</pre>
<h2>Button setup</h2>
<p>The message box comes with 1-3 buttons, each with a <code>DialogResult</code> 
value. Buttons are customized using the <code>SetButtons</code> method. By 
default, if this method is not called, then an OK button is created. This method 
is overloaded. The simplest one takes <code>String[]</code> button captions (the 
string array must have a length between 1 and 3). These buttons will have a
<code>DialogResult</code> value <code>DialogResult.None</code>. To determine 
which button was clicked, you can use the <code>DialogBoxResult</code> property.</p>
<pre>//Sets 2 buttons with DialogResult.None.
MsgBox db = new MsgBox("This is a test message", 
                "This is a test title", 
                MessageBoxIcon.Information);
db.SetButtons("First Button", "Second Button");
db.ShowDialog(this); //show the message box. Does not return a DialogResult value.
//Returns what button was pressed. DialogBoxResult.Button1 or DialogBoxResult.Button2
DialogBoxResult result = db.DialogBoxResult;</pre>
Another variation also takes <code>DialogResult[]</code> that assigns a <code>
DialogResult</code> value to a button (the array must be the same length as 
captions) and the default button number (must be 1-3). To determine which button 
was clicked, <code>DialogResult</code> is returned from <code>ShowDialog(this)</code>.
<pre>//Sets 2 buttons with DialogResult.None.
MsgBox db = new MsgBox("This is a test message", 
            "This is a test title", MessageBoxIcon.Information);
//by default the second button will be selected.
db.SetButtons(new string[]{"Yes Button.", "No Button"}, 
              new DialogResult[] {DialogResult.Yes, DialogResult.No}, 2);
DialogResult r = db.ShowDialog(this); //show the message box and return the DialogResult.
</pre>
<h2>Checkbox Setup</h2>
The message box also comes with an optional checkbox. By default, the 
checkbox is hidden. Use the <code>SetCheckbox()</code> method to add a checkbox. 
It takes two arguments: the checkbox caption and the default checked value. The
<code>CheckboxChecked</code> property returns whether the checkbox was checked.
<pre>//Box with a checkbox.
MsgBox db = new MsgBox("This is a test message", 
                "This is a test title", MessageBoxIcon.Information);
db.SetCheckbox("Don't show this message again.", false);
db.ShowDialog(this); //shows the message box.
bool isChecked = db.CheckboxChecked;
//returns whether the checkbox was checked by user.
</pre>
<h2>Inner Workings (Optional)</h2>
This is a brief summary of what is going on behind the scenes. The <code>
MsgBox</code> class inherits from the <code>System.Windows.Forms.Form</code> 
class. Initially, the form has three button controls, a checkbox control, and a 
label control. The buttons and checkbox are hidden and become visible when the
<code>SetButtons()</code> and <code>SetCheckbox()</code> methods are called.<br><br>
The most interesting part is auto sizing the form. The form must fit all the 
controls (and add proper spacing and margins). The controls will have unknown 
sizes because they will depend on the text assigned to them. For instance, the 
buttons will vary in width depending on the captions set in the <code>
SetButtons()</code> method. The trick is to set the <code>Control.AutoSize</code> 
property to <code>true</code> on all the controls in order to auto-size the 
control. Once the controls are set up and the user calls <code>ShowDialog()</code> 
method, the <code>Form.Load</code> event is fired and my <code>setDialogSize()</code> 
method is called. This method adds up all the controls' height/width (from <code>
Control.Size.Height</code> and <code>Control.Size.Width</code> properties), plus 
all the spacing and margins, and sets the <code>Form</code>'s height and width.<br><br>
The final step is the button/checkbox row placement. In the <code>Form.Load</code> 
event handler, there is a call to my <code>setButtonRowLocations()</code> 
method. This calculates the placement of the buttons in the bottom right corner 
based on their widths and the checkbox is always placed in the bottom left 
corner.<br><br>
Refer to the source files for the full implementation of the <code>
setDialogSize()</code> and <code>setButtonRowLocations()</code> methods.<br>
<h2>Other mentions</h2>
The message box also has a <code>SetMinSize()</code> method which sets the 
minimum dimensions of the message box. If the text size is greater than the 
minimum size, then the message box will increase in size in order to fit the 
text. I would recommend using this method if the message is really small, but 
you don't want the message box to be small.<br><br>
The <code>ShowDialog()</code> method used in all examples is a standard <code>
Form.ShowDialog()</code> method. Use the proper version of this method to 
accommodate your code.<br><br>
This class inherits the <code>Form</code> class so it is customizable. For 
instance, you can easily set the <code>MsgBox.Icon</code> property to add an 
icon in the title bar.
