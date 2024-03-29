Mavzu: Face Detector


<!DOCTYPE html>

<html lang="en">

<head>

`  `<meta charset="UTF-8">

`  `<meta name="viewport" content="width=device-width, initial-scale=1.0">

`  `<meta http-equiv="X-UA-Compatible" content="ie=edge">

`  `<title>Face detection on the browser using javascript !</title>

`  `<script defer src="face-api.min.js"></script>

`  `<script defer src="script.js"></script>

`  `<link rel="stylesheet" href="style.css">

</head>

<body>

`  `<video id="video" width="600" height="450" autoplay>

</body>

</html>

CSS:

body {

`    `padding: 0;

`    `margin: 0;

`    `width: 100vw;

`    `height: 100vh;

`    `display: flex;

`    `align-items: center;

`    `justify-content: center;

}

canvas {

`    `position: absolute;

`    `border-color: red;

}

video{

`    `border-radius: 10px;

`    `width: 100%;

`    `height: 100%;

}


Bu qism saytni korinishiga javob beradi  va asosiy qism js da bajriladi quyidagicha beriladi:

const video = document.getElementById("video");

Promise.all([

`  `faceapi.nets.ssdMobilenetv1.loadFromUri("/models"),

`  `faceapi.nets.faceRecognitionNet.loadFromUri("/models"),

`  `faceapi.nets.faceLandmark68Net.loadFromUri("/models"),

]).then(startWebcam);

function startWebcam() {

`  `navigator.mediaDevices

.getUserMedia({

`      `video: true,

`      `audio: false,

`    `})

.then((stream) => {

`      `video.srcObject = stream;

`    `})

.catch((error) => {

`      `console.error(error);

`    `});

}

function getLabeledFaceDescriptions() {

`  `const labels = ["Hayotjon","Diyorcha"];

`  `return Promise.all(

`    `labels.map(async (label) => {

`      `const descriptions = [];

`      `for (let i = 1; i <= 2; i++) {

`        `const img = await faceapi.fetchImage(`./labels/${label}/${i}.png`);

`        `const detections = await faceapi

.detectSingleFace(img)

.withFaceLandmarks()

.withFaceDescriptor();

`        `descriptions.push(detections.descriptor);

`      `}

`      `return new faceapi.LabeledFaceDescriptors(label, descriptions);

`    `})

`  `);

}

video.addEventListener("play", async () => {

`  `const labeledFaceDescriptors = await getLabeledFaceDescriptions();

`  `const faceMatcher = new faceapi.FaceMatcher(labeledFaceDescriptors);

`  `const canvas = faceapi.createCanvasFromMedia(video);

`  `document.body.append(canvas);

`  `const displaySize = { width: video.width, height: video.height };

`  `faceapi.matchDimensions(canvas, displaySize);

`  `setInterval(async () => {

`    `const detections = await faceapi

.detectAllFaces(video)

.withFaceLandmarks()

.withFaceDescriptors();

`    `const resizedDetections = faceapi.resizeResults(detections, displaySize);

`    `canvas.getContext("2d").clearRect(0, 0, canvas.width, canvas.height);

`    `const results = resizedDetections.map((d) => {

`      `return faceMatcher.findBestMatch(d.descriptor);

`    `});

`    `results.forEach((result, i) => {

`      `const box = resizedDetections[i].detection.box;

`      `const drawBox = new faceapi.draw.DrawBox(box, {

`        `label: result,

`      `});

`      `drawBox.draw(canvas);

`    `});

`  `}, 100);

});


Natija: 

![](Aspose.Words.a48c8567-e06e-439f-89dc-22be38b87874.001.png)

![](Aspose.Words.a48c8567-e06e-439f-89dc-22be38b87874.002.png)

Bu dasturni qismida kirtilgan shaxsning yuzni taniydi va kimligini korsatadi,  shu bilan birgalikda bu dastur qismi shaxsining suratini ham taniydi.

Bu dastur assosiy qism ishi javascript da bajariladi va bu qismda yuzni tanish va uni kimligini chiqarish qismi hisoblanadi.

Ushbu kod veb-brauzerda ishlaydigan yuzni tanish dasturini aniqlaydi. Men ba'zi funktsiyalarni quyidagicha tushuntirishim mumkin:

1\. `startWebcam()`: Bu funksiya brauzerda mavjud veb-kamerani ishga tushiradi va video oqimini qabul qiladi. Foydalanuvchidan kamerasiga kirish uchun ruxsat oladi.

2\. `getLabeledFaceDescriptions()`: Bu funksiya yorliqli yuz tavsiflarini oladi. Teglar - bu muayyan odamlarning ismlarini o'z ichiga olgan bir qator teg nomlari. Har bir teg uchun ma'lum miqdordagi yuz tasvirlari mavjud va bu tasvirlardan yuz tavsiflari olinadi.

3\. `video.addEventListener("play", async () => {...})`: Ushbu hodisa tinglovchisi video o'ynay boshlaganda ishlaydi. Bu vaqtda yuz tavsiflari olinadi va video oqimidagi yuzlarga moslashtiriladi. Videoda mos keladigan yuzlar chizilgan.

Va dastur qismining asosida 3 model dan foydalanilgan bular:

1. ` `ssdMobileNetV1
1. faceRecognationNet
1. faceLandmark68V1

Bu kod face-api.js deb nomlangan kutubxona yordamida yuzni tanish funksiyasini taʼminlaydi. Ushbu kutubxona brauzer ichida ishlash uchun mo'ljallangan va veb-kameraga kirish imkonini beruvchi zamonaviy brauzerlar bilan mos keladi. Shu tariqa, brauzer orqali real vaqt rejimida yuzni tanish ilovalarini ishlab chiqish mumkin.


