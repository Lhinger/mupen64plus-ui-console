
# INSTRUCTIONS

## 1. Download one of the N64 roms below (click on the game you want)

## 2. Scroll down to where it says "play" then click the "choose files" button right below it

## 3. Select the rom that you just downloaded, and click "open"



## ROMS

[Mario 64](https://github.com/AlexBlackmore/roms/raw/master/n64/Super%20Mario%2064%20(USA).n64)

[Paper Mario](https://www.mediafire.com/file/l52viyhesl6yl1s/Paper_Mario_%2528USA%2529.n64/file)

[Kirby 64](https://github.com/AlexBlackmore/roms/raw/master/n64/Kirby%2064%20-%20The%20Crystal%20Shards%20(USA).n64)

[Legend of Zelda, Ocarina of time](https://github.com/AlexBlackmore/roms/raw/master/n64/Legend%20of%20Zelda%2C%20The%20-%20Ocarina%20of%20Time%20(USA)%20(Rev%20B).n64)

[Banjo-Kazooie](https://github.com/AlexBlackmore/roms/raw/master/n64/Banjo-Kazooie%20(USA)%20(Rev%20A).n64)

[Gex 3 (i think im gonna puke)](https://github.com/AlexBlackmore/roms/raw/master/n64/Gex%203%20-%20Deep%20Cover%20Gecko%20(USA).n64)


<html>

  <head>
    <meta charset='utf-8' />
    <meta http-equiv="X-UA-Compatible" content="chrome=1" />
    <meta name="description" content="Lhinger's N64 : An online N64 emulator (MADE COMPLETELY BY JQUESNELLE" />

    <link rel="stylesheet" type="text/css" media="screen" href="stylesheets/stylesheet.css">
	
	
	
	</head>

  <body>

   

    <!-- MAIN CONTENT -->
    <div id="main_content_wrap" class="outer">
      <section id="main_content" class="inner">
		
	      
	      
	      
	      
	      <h3>
			Controls
		</h3>
		<p>
			Analog stick: Arrow keys <br/>
			B button: F <br/>
			A button: G <br/>
			Z Trigger: Z <br/>
			L/R Trigger: X,C<br/>
			C buttons U/D/L/R: I,K,J,L<br/>
			Start: Enter <br/>
			DPAD U/D/L/R: W,S,A,D<br/>
		</p>
		<h3>
			Play
		</h3>
		<p>
			<style>
			  .emscripten { padding-right: 0; margin-left: auto; margin-right: auto; display: block; }
			  div.emscripten { text-align: center; }      
			  div.emscripten_border { border: 1px solid black; }
			  /* the canvas *must not* have any border or padding, or mouse coords will be wrong */
			  canvas.emscripten { border: 0px none; }

			  #emscripten_logo {
				display: inline-block;
				margin: 0;
			  }

			  .spinner {
				height: 30px;
				width: 30px;
				margin: 0;
				margin-top: 20px;
				margin-left: 20px;
				display: inline-block;
				vertical-align: top;

				-webkit-animation: rotation .8s linear infinite;
				-moz-animation: rotation .8s linear infinite;
				-o-animation: rotation .8s linear infinite;
				animation: rotation 0.8s linear infinite;

				border-left: 5px solid rgb(235, 235, 235);
				border-right: 5px solid rgb(235, 235, 235);
				border-bottom: 5px solid rgb(235, 235, 235);
				border-top: 5px solid rgb(120, 120, 120);
				
				border-radius: 100%;
				background-color: rgb(189, 215, 46);
			  }

			  @-webkit-keyframes rotation {
				from {-webkit-transform: rotate(0deg);}
				to {-webkit-transform: rotate(360deg);}
			  }
			  @-moz-keyframes rotation {
				from {-moz-transform: rotate(0deg);}
				to {-moz-transform: rotate(360deg);}
			  }
			  @-o-keyframes rotation {
				from {-o-transform: rotate(0deg);}
				to {-o-transform: rotate(360deg);}
			  }
			  @keyframes rotation {
				from {transform: rotate(0deg);}
				to {transform: rotate(360deg);}
			  }

			  #status {
				display: inline-block;
				vertical-align: top;
				margin-top: 30px;
				margin-left: 20px;
				font-weight: bold;
				color: rgb(120, 120, 120);
			  }

			  #progress {
				height: 20px;
				width: 30px;
			  }

			  #controls {
				display: inline-block;
				float: right;
				vertical-align: top;
				margin-top: 30px;
				margin-right: 20px;
			  }

			  #output {
				width: 100%;
				height: 200px;
				margin: 0 auto;
				margin-top: 10px;
				display: block;
				background-color: black;
				color: white;
				font-family: 'Lucida Console', Monaco, monospace;
				outline: none;
			  }
			</style>
			<div class="spinner" id='spinner'></div>
			<div class="emscripten" id="status">Downloading...</div>
			<div id='filechooser' style="visibility:hidden;">
				<input type="file" id="files" name="files[]" multiple />
				<output id="list"></output>
				<script>
				  function handleFileSelect(evt) {
					var files = evt.target.files; // FileList object
					if(files.length != 1)
						return;
					var reader = new FileReader();
					reader.onload = function(e) {
						var rom = files[0].name;
						FS.writeFile(rom, new Uint8Array(e.target.result), {encoding: 'binary'});
						document.getElementById('filechooser').style.visibility = 'hidden';
						Module['callMain'](['--resolution', '640x480', rom]);
					};
					reader.readAsArrayBuffer(files[0]);
					
				  }

				  document.getElementById('files').addEventListener('change', handleFileSelect, false);
				</script>
			</div>
			<div class="emscripten">
			  <progress value="0" max="100" id="progress" hidden=1></progress>
			</div>
			<div class="emscripten_border">
			  <canvas class="emscripten" id="canvas" oncontextmenu="event.preventDefault()"></canvas>
			</div>
			<script type='text/javascript'>
			  var statusElement = document.getElementById('status');
			  var progressElement = document.getElementById('progress');
			  var spinnerElement = document.getElementById('spinner');

			  var Module = {
				preRun: (function() {
					document.getElementById('filechooser').style.visibility = 'visible';
					document.getElementById('spinner').style.display = 'none';
					document.getElementById('status').style.display = 'none';
				})(),
				postRun: [],
				noInitialRun: true,
				print: (function() {
				  var element = document.getElementById('output');
				  if (element) element.value = ''; // clear browser cache
				  return function(text) {
					text = Array.prototype.slice.call(arguments).join(' ');
					// These replacements are necessary if you render to raw HTML
					//text = text.replace(/&/g, "&amp;");
					//text = text.replace(/</g, "&lt;");
					//text = text.replace(/>/g, "&gt;");
					//text = text.replace('\n', '<br>', 'g');
					console.log(text);
					if (element) {
					  element.value += text + "\n";
					  element.scrollTop = element.scrollHeight; // focus on bottom
					}
				  };
				})(),
				printErr: function(text) {
				  text = Array.prototype.slice.call(arguments).join(' ');
				  if (0) { // XXX disabled for safety typeof dump == 'function') {
					dump(text + '\n'); // fast, straight to the real console
				  } else {
					console.error(text);
				  }
				},
				canvas: document.getElementById('canvas'),
				setStatus: function(text) {
				  if (!Module.setStatus.last) Module.setStatus.last = { time: Date.now(), text: '' };
				  if (text === Module.setStatus.text) return;
				  var m = text.match(/([^(]+)\((\d+(\.\d+)?)\/(\d+)\)/);
				  var now = Date.now();
				  if (m && now - Date.now() < 30) return; // if this is a progress update, skip it if too soon
				  if (m) {
					text = m[1];
					progressElement.value = parseInt(m[2])*100;
					progressElement.max = parseInt(m[4])*100;
					progressElement.hidden = false;
					spinnerElement.hidden = false;
				  } else {
					progressElement.value = null;
					progressElement.max = null;
					progressElement.hidden = true;
					if (!text) spinnerElement.style.display = 'none';
				  }
				  statusElement.innerHTML = text;
				},
				totalDependencies: 0,
				monitorRunDependencies: function(left) {
				  this.totalDependencies = Math.max(this.totalDependencies, left);
				  Module.setStatus(left ? 'Preparing... (' + (this.totalDependencies-left) + '/' + this.totalDependencies + ')' : 'All downloads complete.');
				}
			  };
			  Module.setStatus('Downloading...');
			</script>
			<script async type="text/javascript" src="mupen64plus-ui-console.js"></script>
		</p>
    <!-- FOOTER  -->
    <div id="footer_wrap" class="outer">
      <footer class="inner">
        <p class="copyright">mupen64plus via emscripten maintained by <a href="https://github.com/jquesnelle">jquesnelle</a></p>
        <p>Published with <a href="http://pages.github.com">GitHub Pages</a></p>
      </footer>
    </div>

    
<!-- begin wwww.htmlcommentbox.com -->
 <div id="HCB_comment_box"><a href="http://www.htmlcommentbox.com">Widget</a> is loading comments...</div>
 <link rel="stylesheet" type="text/css" href="https://www.htmlcommentbox.com/static/skins/bootstrap/twitter-bootstrap.css?v=0" />
 <script type="text/javascript" id="hcb"> /*<!--*/ if(!window.hcb_user){hcb_user={};} (function(){var s=document.createElement("script"), l=hcb_user.PAGE || (""+window.location).replace(/'/g,"%27"), h="https://www.htmlcommentbox.com";s.setAttribute("type","text/javascript");s.setAttribute("src", h+"/jread?page="+encodeURIComponent(l).replace("+","%2B")+"&mod=%241%24wq1rdBcg%24Wt4Ihjfr7tq.EifLCHDe5."+"&opts=16798&num=10&ts=1649629021535");if (typeof s!="undefined") document.getElementsByTagName("head")[0].appendChild(s);})(); /*-->*/ </script>
<!-- end www.htmlcommentbox.com -->
   
<style>
#HCB_comment_box{border: 2px solid #32CD32; border-radius: 5px; padding: 10px; color:#000000; background:#000000 ;}
.hcb-mod b{color:#32CD32;}
#HCB_comment_box textarea,#HCB_comment_box input.text{border-top:1px solid #32CD32;border-left:1px solid #32CD32;border-bottom:1px solid #32CD32;border-right:1px solid #32CD32;background-color:#000000;}
#HCB_comment_box .hcb-wrapper-half{display:block;width:50%;float:left;}
#HCB_comment_box .hcb-wrapper{clear:both;}
#HCB_comment_box input.text{display:block;width:95%;}
#HCB_comment_box input.submit{border-top:1px solid #FCB1A2;border-left:1px solid #FCB1A2;border-bottom:1px solid #BB2B0F;border-right:1px solid #BB2B0F;background-color:#000000;color:#000;font-weight:bold;cursor:pointer;}
#HCB_comment_box div.comment{color:#32CD32; background:#000000; border:3px double #F26646 ; margin:5px; padding:2px;}
#HCB_comment_box .comment .likes{color: #32CD32;}
#HCB_comment_box .hcb-link{color:#32CD32;text-decoration:none;} 
 #HCB_comment_box blockquote {
    color:#32CD32;
  }
     #HCB_comment_box {
    color:#32CD32;
  }
   
  #HCB_comment_box #hcb_form_content,
  #HCB_comment_box #hcb_form_email,
  #HCB_comment_box #hcb_form_name,
  #HCB_comment_box #hcb_form_website,
  #HCB_comment_box #hcb_submit {
    color:#32CD32;
  }
  
 
  input#hcb_form_name {border-top:1px solid #32CD32;border-left:1px solid #32CD32;border-bottom:1px solid #32CD32;border-right:1px solid #32CD32;background-color:#000000;}
 
    
</style>
