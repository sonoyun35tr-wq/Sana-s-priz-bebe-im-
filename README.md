<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Alperen & Monica 💖</title>
  <style>
    body { margin:0; overflow:hidden; font-family:Arial, sans-serif; background:#000; }
    canvas { display:block; position:absolute; top:0; left:0; width:100%; height:100%; z-index:0; }
    #ui { position:absolute; top:50%; left:50%; transform:translate(-50%,-50%); text-align:center; z-index:10; color:white; pointer-events:none; }
    #ui h1{ font-size:3em; text-shadow:2px 2px 15px #000; margin:0;}
    #ui p{ font-size:1.5em; text-shadow:1px 1px 10px #000; margin:20px 0;}
    button{ pointer-events:auto; background:#fff; color:#ff6f91; border:none; padding:15px 35px; font-size:1.3em; border-radius:15px; cursor:pointer; box-shadow:0 4px 10px rgba(0,0,0,0.3); transition:0.3s; }
    button:hover{ background:#ff6f91; color:#fff; transform:scale(1.1); }
  </style>
</head>
<body>
  <div id="ui">
    <h1>Alperen & Monica 💖</h1>
    <p>Sen benim hayatımın en güzel parçasısın!</p>
    <button onclick="showMessage()">Sürpriz Mesaj!</button>
  </div>
  <audio id="romanticSong" loop autoplay>
    <source src="https://cdn.pixabay.com/download/audio/2022/09/01/audio_9e8b6e5f.mp3?filename=hopeful-love-romantic-2-12.mp3" type="audio/mpeg">
  </audio>
  <script src="https://cdn.jsdelivr.net/npm/three@0.152.2/build/three.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/cannon-es@0.20.0/dist/cannon-es.js"></script>
  <script>
    // Three.js sahne
    let scene = new THREE.Scene();
    let camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight,0.1,1000);
    let renderer = new THREE.WebGLRenderer({antialias:true, alpha:true});
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setClearColor(0x000000,0); 
    document.body.appendChild(renderer.domElement);

    // Cannon.js fizik dünyası
    const world = new CANNON.World();
    world.gravity.set(0,-9.82,0);

    // Yer objesi
    const groundBody = new CANNON.Body({type:CANNON.Body.STATIC, shape:new CANNON.Plane()});
    groundBody.quaternion.setFromEuler(-Math.PI/2,0,0);
    world.addBody(groundBody);

    // Parçacıklar
    let particles=[];
    let particleCount=100;
    let geometry=new THREE.SphereGeometry(0.3,8,8);
    for(let i=0;i<particleCount;i++){
        let material=new THREE.MeshStandardMaterial({color:new THREE.Color(`hsl(${Math.random()*360},80%,60%)`), roughness:0.5, metalness:0.3});
        let mesh=new THREE.Mesh(geometry,material);
        mesh.position.set((Math.random()-0.5)*20,Math.random()*20+5,(Math.random()-0.5)*20);
        scene.add(mesh);

        let body=new CANNON.Body({mass:0.5, shape:new CANNON.Sphere(0.3)});
        body.position.copy(mesh.position);
        world.addBody(body);

        particles.push({mesh,body});
    }

    // Işıklar
    scene.add(new THREE.AmbientLight(0xffffff,0.6));
    const directionalLight=new THREE.DirectionalLight(0xff69b4,0.8);
    directionalLight.position.set(10,20,10);
    scene.add(directionalLight);

    camera.position.z=40;

    // Fare etkisi
    let mouse={x:0,y:0};
    document.addEventListener('mousemove',e=>{
        mouse.x=(e.clientX/window.innerWidth-0.5)*20;
        mouse.y=-(e.clientY/window.innerHeight-0.5)*20;
    });

    // Animasyon döngüsü
    function animate(){
        requestAnimationFrame(animate);
        world.step(1/60);
        particles.forEach(p=>{
            p.body.applyForce(new CANNON.Vec3((mouse.x-p.body.position.x)*0.05,0,(mouse.y-p.body.position.z)*0.05), p.body.position);
            p.mesh.position.copy(p.body.position);
            p.mesh.quaternion.copy(p.body.quaternion);
        });
        renderer.render(scene,camera);
    }
    animate();

    // Mesaj fonksiyonu
    function showMessage(){
        const msg=document.createElement('div');
        msg.textContent="Seni çok seviyorum, Monica! 🌹💖";
        msg.style.position='fixed';
        msg.style.top='50%';
        msg.style.left='50%';
        msg.style.transform='translate(-50%,-50%) scale(0)';
        msg.style.background='rgba(255,111,145,0.95)';
        msg.style.padding='50px 100px';
        msg.style.borderRadius='25px';
        msg.style.fontSize='2.5em';
        msg.style.color='white';
        msg.style.boxShadow='0 0 120px rgba(0,0,0,0.95)';
        msg.style.transition='transform 0.5s, opacity 0.5s';
        msg.style.opacity='0';
        document.body.appendChild(msg);
        setTimeout(()=>{msg.style.transform='translate(-50%,-50%) scale(1.7)';msg.style.opacity='1';},50);
        setTimeout(()=>{msg.style.transform='translate(-50%,-50%) scale(1)';},900);
        setTimeout(()=>{msg.remove();},7000);

        for(let i=0;i<50;i++){
            let mini=document.createElement('div');
            mini.textContent=Math.random()<0.5?'💖':'✨';
            mini.style.position='fixed';
            mini.style.left=(window.innerWidth/2+Math.random()*300-150)+'px';
            mini.style.top=(window.innerHeight/2+Math.random()*300-150)+'px';
            mini.style.fontSize=(Math.random()*28+18)+'px';
            mini.style.opacity=1;
            mini.style.transition='all 1.5s ease-out';
            document.body.appendChild(mini);
            setTimeout(()=>{mini.style.transform='translateY(-150px) scale(0)';mini.style.opacity=0;},50);
            setTimeout(()=>{mini.remove();},1550);
        }
    }
  </script>
</body>
</html>
