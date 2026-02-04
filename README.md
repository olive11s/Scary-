# Scary-
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Escape the Hotel - Giselle ❤️</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; touch-action: manipulation; }
        body, html { width: 100%; height: 100%; overflow: hidden; background: #000; font-family: 'Courier New', Courier, monospace; color: white; }
        
        /* Perspective Hallway */
        #game-screen {
            position: relative; width: 100%; height: 100%;
            display: flex; flex-direction: column; justify-content: center; align-items: center;
            background: linear-gradient(to bottom, #1a1a1a 0%, #000 100%);
            perspective: 1000px;
        }

        /* 3D Hallway Effect */
        .hallway {
            position: absolute; width: 100%; height: 100%;
            border-left: 50px solid #222; border-right: 50px solid #222;
            z-index: 1; pointer-events: none;
        }

        /* Doors */
        .door-container { display: flex; gap: 20px; z-index: 10; margin-top: 50px; }
        .door {
            width: 80px; height: 140px; background: #4a3728;
            border: 4px solid #2a1d15; border-radius: 5px;
            display: flex; justify-content: center; align-items: center;
            font-size: 20px; cursor: pointer; transition: transform 0.2s;
        }
        .door:active { transform: scale(0.9); }

        /* HUD */
        #hud { position: absolute; top: 10%; width: 100%; display: flex; justify-content: space-around; font-weight: bold; z-index: 20; }
        .life { color: #ff4d4d; font-size: 24px; }

        /* Monster "Look Back" Screen */
        #monster-view {
            display: none; position: fixed; inset: 0; 
            background: url('https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExNHJndnd6bmRxcXN6bnd6bmRxeHpseHpseHpseHpseHpseHpseHpsJmVwPXYxX2ludGVybmFsX2dpZl9ieV9pZCZjdD1n/3o7TKsWZyGKY1cKG1W/giphy.gif') no-repeat center center;
            background-size: cover; z-index: 100;
        }

        /* Final Scenes */
        #chest-scene, #shooting-scene, #ending { display: none; position: fixed; inset: 0; background: #000; z-index: 150; flex-direction: column; justify-content: center; align-items: center; text-align: center; padding: 20px; }
        .meteor { width: 100px; height: 100px; background: orange; border-radius: 50%; box-shadow: 0 0 50px red; position: absolute; top: -150px; transition: top 1s ease-in; }

        .btn { padding: 15px 30px; background: #880e4f; color: white; border: none; border-radius: 5px; font-weight: bold; margin-top: 20px; }
    </style>
</head>
<body>

<div id="game-screen">
    <div class="hallway"></div>
    <div id="hud">
        <div id="lives-display">Lives: ❤️❤️❤️</div>
        <div id="stage-display">Door: 1/7</div>
    </div>

    <h2 id="instruction">Pick the right door!</h2>
    <div class="door-container" id="doors">
        <div class="door" onclick="checkDoor(0)">1</div>
        <div class="door" onclick="checkDoor(1)">2</div>
        <div class="door" onclick="checkDoor(2)">3</div>
    </div>

    <div style="margin-top: 50px; z-index: 20;">
        <button class="btn" onclick="lookBack()">LOOK BACK 👁️</button>
    </div>
</div>

<div id="monster-view">
    <h1 style="background: rgba(0,0,0,0.7); padding: 20px; position: absolute; bottom: 20%;">HE IS GETTING CLOSER...</h1>
</div>

<div id="chest-scene">
    <h1>You reached the final room!</h1>
    <div style="font-size: 80px; margin: 20px;">🧳</div>
    <button class="btn" onclick="openChest()">OPEN CHEST</button>
</div>

<div id="shooting-scene">
    <h1 style="color:red">MONSTER DETECTED!</h1>
    <div style="font-size: 100px; margin: 20px;">👹</div>
    <button class="btn" onclick="shootMonster()" style="background:red; font-size: 24px;">🔥 SHOOT! 🔥</button>
</div>

<div id="ending">
    <div id="meteor" class="meteor"></div>
    <h1 id="end-text">Congratulations!<br>You survived the apocalypse!</h1>
</div>

<audio id="scream"><source src="https://assets.mixkit.co/active_storage/sfx/2573/2573-preview.mp3" type="audio/mpeg"></audio>

<script>
    let stage = 1;
    let lives = 3;
    let monsterCloseness = 0;
    let correctDoor = Math.floor(Math.random() * 3);

    function checkDoor(choice) {
        if (choice === correctDoor) {
            stage++;
            if (stage > 7) {
                showChest();
            } else {
                document.getElementById('stage-display').innerText = `Door: ${stage}/7`;
                correctDoor = Math.floor(Math.random() * 3);
                // Flash hallway to simulate running
                document.getElementById('game-screen').style.background = "#444";
                setTimeout(() => document.getElementById('game-screen').style.background = "linear-gradient(to bottom, #1a1a1a 0%, #000 100%)", 100);
            }
        } else {
            loseLife();
        }
    }

    function loseLife() {
        lives--;
        monsterCloseness++;
        updateLives();
        if (lives <= 0) {
            alert("The Monster caught you! Waking up...");
            location.reload();
        } else {
            alert("Wrong door! You hear breathing behind you...");
        }
    }

    function lookBack() {
        const view = document.getElementById('monster-view');
        view.style.display = 'block';
        monsterCloseness++;
        // Zoom the background slightly to show he's closer
        view.style.transform = `scale(${1 + (monsterCloseness * 0.2)})`;
        
        setTimeout(() => {
            view.style.display = 'none';
            if (monsterCloseness >= 5) {
                alert("You looked back too much! He caught you!");
                location.reload();
            }
        }, 1200);
    }

    function updateLives() {
        let hearts = "";
        for(let i=0; i<lives; i++) hearts += "❤️";
        document.getElementById('lives-display').innerText = "Lives: " + hearts;
    }

    function showChest() {
        document.getElementById('game-screen').style.display = 'none';
        document.getElementById('chest-scene').style.display = 'flex';
    }

    function openChest() {
        document.getElementById('chest-scene').style.display = 'none';
        document.getElementById('shooting-scene').style.display = 'flex';
    }

    function shootMonster() {
        document.getElementById('shooting-scene').innerHTML = "<h1 style='color:green'>TARGET NEUTRALIZED</h1>";
        document.getElementById('scream').play();
        setTimeout(() => {
            document.getElementById('shooting-scene').style.display = 'none';
            showEnding();
        }, 2000);
    }

    function showEnding() {
        document.getElementById('ending').style.display = 'flex';
        setTimeout(() => {
            const met = document.getElementById('meteor');
            met.style.top = "50%";
            setTimeout(() => {
                document.body.style.background = "white";
                document.getElementById('end-text').innerHTML = "<h1 style='color:black'>TO BE CONTINUED...</h1>";
                document.getElementById('ending').style.background = "white";
            }, 1000);
        }, 2000);
    }
</script>
</body>
</html>
