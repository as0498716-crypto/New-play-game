
<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Monster Trainer 3D</title>
<style>
body{margin:0;background:#111;color:white;text-align:center;font-family:Arial}
canvas{display:block}
button{padding:12px;margin:5px;border-radius:10px;font-size:16px}
#controls{position:fixed;bottom:15px;width:100%}
</style>
</head>
<body>

<h2>🐉 Monster Trainer 3D</h2>
<p id="info">Monster ko pakdo!</p>

<div id="controls">
<button onclick="move(0,1)">⬆️</button><br>
<button onclick="move(-1,0)">⬅️</button>
<button onclick="move(0,-1)">⬇️</button>
<button onclick="move(1,0)">➡️</button><br>
<button onclick="catchMonster()">🎯 Catch</button>
<button onclick="battle()">⚔️ Battle</button>
<button onclick="heal()">❤️ Heal</button>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x87ceeb);

const camera = new THREE.PerspectiveCamera(
  75, window.innerWidth/window.innerHeight, 0.1, 1000
);
camera.position.set(0,6,9);
camera.lookAt(0,0,0);

const renderer = new THREE.WebGLRenderer({antialias:true});
renderer.setSize(window.innerWidth,window.innerHeight);
document.body.appendChild(renderer.domElement);

scene.add(new THREE.HemisphereLight(0xffffff,0x444444,2));

const ground = new THREE.Mesh(
  new THREE.PlaneGeometry(100,100),
  new THREE.MeshStandardMaterial({color:0x228b22})
);
ground.rotation.x=-Math.PI/2;
scene.add(ground);

// Player
const player = new THREE.Group();

const body = new THREE.Mesh(
  new THREE.BoxGeometry(1,1.5,0.7),
  new THREE.MeshStandardMaterial({color:0x0000ff})
);
body.position.y=1;
player.add(body);

const head = new THREE.Mesh(
  new THREE.SphereGeometry(0.45,16,16),
  new THREE.MeshStandardMaterial({color:0xffcc99})
);
head.position.y=2;
player.add(head);

scene.add(player);

// Monster
const monster = new THREE.Group();

const monsterBody = new THREE.Mesh(
  new THREE.SphereGeometry(0.8,20,20),
  new THREE.MeshStandardMaterial({color:0xff3333})
);
monsterBody.position.y=0.8;
monster.add(monsterBody);

const eye = new THREE.Mesh(
  new THREE.SphereGeometry(0.15,12,12),
  new THREE.MeshStandardMaterial({color:0xffffff})
);
eye.position.set(0.3,1.1,0.6);
monster.add(eye);

monster.position.set(4,0,-4);
scene.add(monster);

let hp=100;
let caught=false;

function move(x,z){
  player.position.x+=x;
  player.position.z+=z;
}

function catchMonster(){
  let distance=player.position.distanceTo(monster.position);

  if(distance<3){
    caught=true;
    monster.visible=false;
    document.getElementById("info").innerText=
      "🎉 Monster caught! You are a Trainer!";
  }else{
    document.getElementById("info").innerText=
      "Monster ke paas jao!";
  }
}

function battle(){
  if(caught){
    document.getElementById("info").innerText=
      "🏆 You won the battle!";
  }else{
    hp-=10;
    document.getElementById("info").innerText=
      "Battle! Player HP: "+hp;
    if(hp<=0){
      document.getElementById("info").innerText=
        "You fainted! Press Heal.";
    }
  }
}

function heal(){
  hp=100;
  document.getElementById("info").innerText=
    "❤️ HP restored to 100!";
}

function animate(){
  requestAnimationFrame(animate);

  camera.position.x=player.position.x;
  camera.position.z=player.position.z+9;
  camera.lookAt(player.position.x,1,player.position.z);

  if(!caught){
    monster.rotation.y+=0.01;
  }

  renderer.render(scene,camera);
}
animate();

window.addEventListener("resize",()=>{
  camera.aspect=window.innerWidth/window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth,window.innerHeight);
});
</script>
</body>
</html>
