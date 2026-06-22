To get the targets readable for the AR library upload the traget images to: https://hiukim.github.io/mind-ar-js-doc/tools/compile/ 

Upload your video to supabase and add the links in the same order as the images to mind ar


<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <!-- Production-grade libraries -->
    <script src="https://aframe.io/releases/1.4.2/aframe.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/mind-ar@1.2.2/dist/mindar-image-aframe.prod.js"></script>
    
    <style>
      body { margin: 0; overflow: hidden; background-color: #111; font-family: -apple-system, sans-serif; }
      
      /* Overlay to manage browser permissions cleanly */
      #overlay {
        position: fixed; top: 0; left: 0; right: 0; bottom: 0;
        background: #1a1a1a; display: flex; flex-direction: column;
        align-items: center; justify-content: center; z-index: 9999; color: white; padding: 20px; text-align: center;
      }
      button {
        background: #C67846; color: black; border: none; padding: 14px 28px;
        font-size: 18px; font-weight: bold; border-radius: 30px; cursor: pointer; margin-top: 20px;
        box-shadow: 0 4px 15px rgba(198, 120, 70,0.4);
      }
      #status-text { font-size: 14px; color: #aaa; margin-top: 15px; max-width: 280px; line-height: 1.4; }
    </style>
  </head>
  <body>

    <!-- The User-Interaction Screen -->
    <div id="overlay">
      <h2>Animated Postcard</h2>
      <p id="status-text">Ready to scan. Please tap below to allow camera access.</p>
      <button id="start-btn">Open Scanner</button>
    </div>

    <!-- The AR Environment (auto-starts only after button click) -->
    <a-scene 
      mindar-image="imageTargetSrc: ./targets.mind; autoStart: false; uiLoading: no; uiScanning: no;" 
      color-space="sRGB" 
      renderer="colorManagement: true, physicallyCorrectLights" 
      vr-mode-ui="enabled: false" 
      device-orientation-permission-ui="enabled: false">
      
      <a-camera position="0 0 0" look-controls="enabled: false"></a-camera>

      <a-entity mindar-image-target="targetIndex: 0">
        <!-- Rotating Blue Test Box -->
        <a-box color="#007bff" position="0 0 0" scale="0.4 0.4 0.4" animation="property: rotation; to: 0 360 0; loop: true; dur: 4000"></a-box>
      </a-entity>
    </a-scene>

    <script>
      const startBtn = document.getElementById('start-btn');
      const overlay = document.getElementById('overlay');
      const statusText = document.getElementById('status-text');
      const sceneEl = document.querySelector('a-scene');

      startBtn.addEventListener('click', () => {
        statusText.innerText = "Initializing camera... If this fails, please copy this link into Safari or Chrome.";
        startBtn.style.display = 'none';

        // Direct programmatic boot up of the AR Engine
        const arSystem = sceneEl.systems["mindar-image-system"];
        
        // Safety timeout check for privacy engines like DuckDuckGo
        const safetyTimeout = setTimeout(() => {
          statusText.innerHTML = "<strong>Browser Blocked Camera</strong><br><br>Privacy-focused browsers block WebAR. Please open this link using <strong>Safari (iPhone)</strong> or <strong>Chrome (Android)</strong>.";
        }, 4000);

        sceneEl.addEventListener('renderstart', () => {
          clearTimeout(safetyTimeout);
          overlay.style.display = 'none'; // Engine started! Hide the menu.
        });

        arSystem.start(); 
      });
    </script>
  </body>
</html>
