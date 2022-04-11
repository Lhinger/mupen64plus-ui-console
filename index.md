<!DOCTYPE html>
<html>

  <head>
    <meta charset='utf-8' />
    <meta http-equiv="X-UA-Compatible" content="chrome=1" />
    <meta name="description" content="mupen64plus via emscripten : A port of the popular Nintendo 64 emulator for the web" />

    <link rel="stylesheet" type="text/css" media="screen" href="stylesheets/stylesheet.css">

    <title>mupen64plus via emscripten</title>
  </head>

  <body>

    <!-- HEADER -->
    <div id="header_wrap" class="outer">
        <header class="inner">
          <a id="forkme_banner" href="https://github.com/jquesnelle/mupen64plus-ui-console/tree/emscripten">View on GitHub</a>

          <h1 id="project_title">mupen64plus via emscripten</h1>
          <h2 id="project_tagline">A port of the popular Nintendo 64 emulator for the web</h2>

            <section id="downloads">
              <a class="zip_download_link" href="https://github.com/jquesnelle/mupen64plus-ui-console/zipball/emscripten">Download this project as a .zip file</a>
              <a class="tar_download_link" href="https://github.com/jquesnelle/mupen64plus-ui-console/tarball/emscripten">Download this project as a tar.gz file</a>
            </section>
        </header>
    </div>

    <!-- MAIN CONTENT -->
    <div id="main_content_wrap" class="outer">
      <section id="main_content" class="inner">
        <h3>
			Basic info
		</h3>
		<p>
			This is a port of <a href="https://code.google.com/p/mupen64plus/">mupen64plus</a>, the popular Nintendo 64 emulator for the web. It uses <a href="https://github.com/kripken/emscripten">emscripten</a> 
			to compile the C/C++ code of mupen64plus to <a href="http://asmjs.org/">asm.js</a>, a subset of Javascript meant to be highly optimizable. This port is currently very immature with lots of work left to 
			be done; for example, gamepads, save states and customizable controls are not supported and there are also many graphical glitches in games. Furthermore, it requires <a href="http://www.mozilla.org/en-US/firefox/new/">Firefox</a>, although support for Chrome 
			should be coming soon.
		</p>
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

    

  </body>
</html>