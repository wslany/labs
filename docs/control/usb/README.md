# USB Power Trigger

<script src="../../jquery.min.js"></script>
<script src="../../qrcodeborder.js"></script>
<script src="../../html2canvas.min.js"></script>
<style>
        #qrcode{
            width: 100%;
        }
        div{
            width: 100%;
            display: inline-block;
        }
</style>

The camera can use the switching on of USB power to boot the camera, and perform an action of your choice.

The command to perform: <input type="text" id="addcmd" value="!S">  e.g. **!S** Will start capture in the camera's default mode. You can make you own command using many of the features within the [**GoPro QR Code Creator**](../custom).

Start command <input type="range" style="width: 200px;" id="tlsec" name="tlsec" min="0" max="60" value="0"><label for="tlsec"></label>&nbsp;&nbsp;<b id="secstext"></b> seconds after USB power,<br> 
and end after <input type="range" style="width: 200px;" id="tlendsec" name="tlendsec" min="0" max="60" value="2"><label for="tlendsec"></label>&nbsp;&nbsp;<b id="secsendtext"></b> seconds after USB power is off.

**Note:** A battery is required, as the camera needs close captured video when power is removed. Unfortunately this means your battery will eventually discharge, so it is best to have a spare battery if you intended to used this feature as a dedicated dash-cam.  

<input type="checkbox" id="repeat" name="repeat" checked> 
<label for="repeat">Repeat for the next USB power on event</label><br>

<div id="qrcode_txt" style="width: 360px">
 <center>
  <div id="qrcode"></div><br>
  <b><font color="#009FDF">GoProQR:</font></b> <em id="qrtext"></em><br>
  <b><font color="#005CAC">USB Power Trigger</font></b>
 </center>
</div>
<button id="copyImg">Copy Image to Clipboard</button>
<br>
<br>
Share this QR Code as a URL: <small id="urltext"></small><br>
<button id="copyBtn">Copy URL to Clipboard</button>
        
**Compatibility:** Labs enabled HERO7 (limited), HERO8, HERO9, HERO10 and MAX 
        
## ver 1.03
[More features](..) for Labs enabled cameras

<script>
var once = true;
var qrcode;
var cmd = "oC15dTmNLeA";
var clipcopy = "";
var lasttimecmd = "";
var changed = true;

function dcmd(cmd, id) {
    var x;
	if(document.getElementById(id) !== null)
	{
		x = document.getElementById(id).checked;
		if( x === true)
			cmd = cmd + document.getElementById(id).value;
	}
	else
	{
	    var i;
		for (i = 1; i < 15; i++) { 
			var newid = id+i;
			if(document.getElementById(newid) !== null)
			{
				x = document.getElementById(newid).checked;
				if( x === true)
					cmd = cmd + document.getElementById(newid).value;
			}
		}
	}
	return cmd;
}

function makeQR() 
{	
  if(once === true)
  {
    qrcode = new QRCode(document.getElementById("qrcode"), 
    {
      text : "!oMBURN=\"\"",
      width : 360,
      height : 360,
      correctLevel : QRCode.CorrectLevel.M
    });
    once = false;
  }
}

function checkTime(i) {
    if (i < 10) {i = "0" + i;}  // add zero in front of numbers < 10
    return i;
}

function timeLoop()
{
  if(document.getElementById("tlsec") !== null)
  {
	cmd = "";
				
	var secs = parseInt(document.getElementById("tlsec").value);	
	document.getElementById("secstext").innerHTML = secs;	
		
	var endsecs = parseInt(document.getElementById("tlendsec").value);	
	endsecs *= 5;
	document.getElementById("secsendtext").innerHTML = endsecs;	
	
	if(secs > 0)
		cmd = cmd + "!u" + secs + "N";
	else
		cmd = cmd + "!uN";
		
	if(document.getElementById("addcmd") !== null)
	{
		cmd = cmd + document.getElementById("addcmd").value;
	}
	
	if(endsecs > 0)
		cmd = cmd + "!u" + endsecs + "E";	
	else
		cmd = cmd + "!uE";
	
    if(document.getElementById("repeat") !== null)
    {
      if(document.getElementById("repeat").checked === true)
      {
        cmd = cmd + "!R";
      }
    }
  }
  
  qrcode.clear(); 
  qrcode.makeCode(cmd);
  
  if(cmd != lasttimecmd)
  {
	changed = true;
	lasttimecmd = cmd;
  }
	
  if(changed === true)
  {
	document.getElementById("qrtext").innerHTML = cmd;
	clipcopy = "https://gopro.github.io/labs/control/set/?cmd=" + cmd + "&title=USB%20Power%20Trigger";
	document.getElementById("urltext").innerHTML = clipcopy;
	changed = false;
  }
	
  var t = setTimeout(timeLoop, 50);
}

function myReloadFunction() {
  location.reload();
}


async function copyImageToClipboard() {
    html2canvas(document.querySelector("#qrcode_txt")).then(canvas => canvas.toBlob(blob => navigator.clipboard.write([new ClipboardItem({'image/png': blob})])));
}
async function copyTextToClipboard(text) {
	try {
		await navigator.clipboard.writeText(text);
	} catch(err) {
		alert('Error in copying text: ', err);
	}
}

function setupButtons() {	
    document.getElementById("copyBtn").onclick = function() { 
        copyTextToClipboard(clipcopy);
	};
    document.getElementById("copyImg").onclick = function() { 
        copyImageToClipboard();
	};
}

makeQR();
setupButtons();
timeLoop();


</script>
