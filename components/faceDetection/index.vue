<template>
  <div>
    <div id="cameraFrame" class="camera-frame">

      <div class="cameraFrame-video">
        <div id="canvas">

        </div>
        <video id="video" autoplay playsinline muted></video>
        <div id="prediction"></div>

        <div class="frame top-left-frame"/>
        <div class="frame top-right-frame"/>
        <div class="frame bottom-left-frame"/>
        <div class="frame bottom-right-frame"/>

      </div>

      <div class="cameraFrame-images">
        <div class="cameraFrame-images-item d-flex align-items-center" v-for="item in faceModels"
             v-if="item.inProgress">
          <div class="d-flex align-items-center justify-content-between">
            <div class="images-item-emoji" v-html="item.icon"></div>
            <div class="images-item-description px-2">
              <span>{{item.name}}</span>
              <p class="m-0">Please make your face {{item.name}} </p>
            </div>
          </div>

          <div class="images-progress">
            <img v-if="item.file" :src="item.file">
            <span v-else>
                            {{detectedFaceCounter.length * 10+' % '}}
                        </span>
          </div>

        </div>

        <div class="cameraFrame-images-gallery">
          <div class="gallery-item" v-for="item in faceModels" v-if="item.file">
            <div class="images-item-emoji" v-html="item.icon">
            </div>
            <img v-if="item.file" :src="item.file">
          </div>


        </div>

      </div>

    </div>

    <div class="detectError" v-if="cantDetectFaceError">
      Your face is not recognizable. Please change the angle of your face and stand where your face is visible.
    </div>
  </div>
</template>

<script>
  import * as faceapi from 'face-api.js';

  export default {
    name: "snapshot",
    data: () => ({
      gender: null,
      age: null,
      faceModels: [
        {
          id: 1,
          model: 'neutral',
          name: 'neutral',
          icon: `<svg width="48" height="48" viewBox="0 0 59 59" fill="none" xmlns="http://www.w3.org/2000/svg">
                                <path d="M17.5184 36.8726H40.8518V41.5392H17.5184V36.8726Z" fill="#FDFDFD"
                                      stroke="#16132E"
                                      stroke-width="2"/>
                                <path d="M29.1851 1.87256C13.7455 1.87256 1.18506 14.433 1.18506 29.8726C1.18506 45.3121 13.7455 57.8726 29.1851 57.8726C44.6246 57.8726 57.1851 45.3121 57.1851 29.8726C57.1851 14.433 44.6246 1.87256 29.1851 1.87256ZM29.1851 53.2059C16.3193 53.2059 5.85173 42.7384 5.85173 29.8726C5.85173 17.0068 16.3193 6.53923 29.1851 6.53923C42.0509 6.53923 52.5184 17.0068 52.5184 29.8726C52.5184 42.7384 42.0509 53.2059 29.1851 53.2059Z"
                                      fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
                                <path d="M17.5184 22.8726H22.1851V27.5392H17.5184V22.8726Z" fill="#FDFDFD"
                                      stroke="#16132E"
                                      stroke-width="2"/>
                                <path d="M36.1851 22.8726H40.8517V27.5392H36.1851V22.8726Z" fill="#FDFDFD"
                                      stroke="#16132E"
                                      stroke-width="2"/>
                            </svg>`,
          file: null,
          inProgress: true
        },

        {
          id: 2,
          model: 'angry',
          name: 'angry',
          icon: `<svg width="48" height="48" viewBox="0 0 58 59" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M30.7015 17.1165L39.3842 12.6953L41.4342 16.8692L32.7515 21.2904L30.7015 17.1165Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M16.9504 16.8731L18.9998 12.6992L27.6826 17.1198L25.6326 21.2937L16.9504 16.8731Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M29.2032 34.4834C25.2688 34.4834 21.4772 35.9366 18.5253 38.5747L16.8032 40.1134L19.8273 43.6202L21.5494 42.0815C23.6642 40.1914 26.3834 39.1501 29.2049 39.1501C32.028 39.1501 34.741 40.1868 36.8486 42.0701L38.5707 43.6088L41.5947 40.1031L39.8721 38.5627C36.9275 35.932 33.1392 34.4834 29.2032 34.4834Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M29.192 1.82812C14.0282 1.82812 1.69202 14.3886 1.69202 29.8281C1.69202 45.2677 14.0282 57.8281 29.192 57.8281C44.3547 57.8281 56.692 45.2677 56.692 29.8281C56.692 14.3886 44.3547 1.82812 29.192 1.82812ZM29.192 53.1615C16.556 53.1615 6.27535 42.6939 6.27535 29.8281C6.27535 16.9623 16.556 6.49479 29.192 6.49479C41.8281 6.49479 52.1087 16.9623 52.1087 29.8281C52.1087 42.6939 41.8281 53.1615 29.192 53.1615Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M17.7336 22.8281H22.317V27.4948H17.7336V22.8281Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M36.067 22.8281H40.6503V27.4948H36.067V22.8281Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
</svg>`,
          file: null,
          inProgress: false
        },
        {
          id: 3,
          model: 'happy',
          name: 'happy',
          icon: `<svg width="48" height="48" viewBox="0 0 58 59" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M29.185 45.5147C35.5033 45.5147 40.6433 40.2812 40.6433 33.848V31.5146H36.06V33.848C36.06 37.7086 32.9767 40.848 29.185 40.848C25.3934 40.848 22.31 37.7086 22.31 33.848V31.5146H17.7267V33.848C17.7267 40.2812 22.8667 45.5147 29.185 45.5147Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M29.1851 57.1812C44.3478 57.1812 56.6851 44.6207 56.6851 29.1812C56.6851 13.7416 44.3478 1.18115 29.1851 1.18115C14.0223 1.18115 1.68506 13.7416 1.68506 29.1812C1.68506 44.6207 14.0223 57.1812 29.1851 57.1812ZM29.1851 5.84782C41.8211 5.84782 52.1017 16.3153 52.1017 29.1812C52.1017 42.047 41.8211 52.5145 29.1851 52.5145C16.549 52.5145 6.26839 42.047 6.26839 29.1812C6.26839 16.3153 16.549 5.84782 29.1851 5.84782Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M17.7267 22.1812H22.31V26.8478H17.7267V22.1812Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M36.0601 22.1812H40.6434V26.8478H36.0601V22.1812Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
</svg>`,
          file: null,
          inProgress: false
        },
        {
          id: 4,
          model: 'surprised',
          name: 'surprised',
          icon: `<svg width="48" height="48" viewBox="0 0 59 59" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M15.704 16.6694L13.1166 20.5522L19.1459 24.5712L13.1166 28.5902L15.704 32.473L27.5575 24.5712L15.704 16.6694Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M42.6661 16.6694L30.8126 24.5712L42.6661 32.473L45.2535 28.5902L39.2242 24.5712L45.2535 20.5522L42.6661 16.6694Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M36.9798 35.651C34.8259 37.5411 32.0585 38.5824 29.1851 38.5824C26.3106 38.5824 23.5477 37.5457 21.4024 35.6624L19.649 34.1237L16.5699 37.6294L18.3234 39.1698C21.3192 41.8005 25.1764 43.2491 29.1851 43.2491C33.1927 43.2491 37.0533 41.7959 40.0571 39.1578L41.8105 37.618L38.7321 34.1123L36.9798 35.651Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M29.1851 1.23779C13.7467 1.23779 1.18506 13.7983 1.18506 29.2378C1.18506 44.6773 13.7467 57.2378 29.1851 57.2378C44.6235 57.2378 57.1851 44.6773 57.1851 29.2378C57.1851 13.7983 44.6235 1.23779 29.1851 1.23779ZM29.1851 52.5711C16.3193 52.5711 5.85173 42.1036 5.85173 29.2378C5.85173 16.372 16.3193 5.90446 29.1851 5.90446C42.0509 5.90446 52.5184 16.372 52.5184 29.2378C52.5184 42.1036 42.0509 52.5711 29.1851 52.5711Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
</svg>`,
          file: null,
          inProgress: false
        },
        {
          id: 5,
          model: 'sad',
          name: 'sad',
          icon: `<svg width="48" height="48" viewBox="0 0 58 59" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M29.1962 33.9502C25.2619 33.9502 21.4702 35.4034 18.5184 38.0415L16.7963 39.5802L19.8203 43.087L21.5424 41.5483C23.6573 39.6582 26.3764 38.6169 29.1979 38.6169C32.0211 38.6169 34.734 39.6536 36.8416 41.5369L38.5637 43.0756L41.5878 39.5699L39.8651 38.0295C36.9205 35.3988 33.1322 33.9502 29.1962 33.9502Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M29.1851 1.29492C14.0212 1.29492 1.68506 13.8554 1.68506 29.2949C1.68506 44.7345 14.0212 57.2949 29.1851 57.2949C44.3478 57.2949 56.6851 44.7345 56.6851 29.2949C56.6851 13.8554 44.3478 1.29492 29.1851 1.29492ZM29.1851 52.6283C16.549 52.6283 6.26839 42.1607 6.26839 29.2949C6.26839 16.4291 16.549 5.96159 29.1851 5.96159C41.8211 5.96159 52.1017 16.4291 52.1017 29.2949C52.1017 42.1607 41.8211 52.6283 29.1851 52.6283Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M17.7267 22.2949H22.31V26.9616H17.7267V22.2949Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M36.0601 22.2949H40.6434V26.9616H36.0601V22.2949Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
</svg>`,
          file: null,
          inProgress: false
        },
        {
          id: 6,
          model: 'fearful',
          name: 'fearful',
          icon: `<svg width="48" height="48" viewBox="0 0 58 59" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M29.1962 33.9502C25.2619 33.9502 21.4702 35.4034 18.5184 38.0415L16.7963 39.5802L19.8203 43.087L21.5424 41.5483C23.6573 39.6582 26.3764 38.6169 29.1979 38.6169C32.0211 38.6169 34.734 39.6536 36.8416 41.5369L38.5637 43.0756L41.5878 39.5699L39.8651 38.0295C36.9205 35.3988 33.1322 33.9502 29.1962 33.9502Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M29.1851 1.29492C14.0212 1.29492 1.68506 13.8554 1.68506 29.2949C1.68506 44.7345 14.0212 57.2949 29.1851 57.2949C44.3478 57.2949 56.6851 44.7345 56.6851 29.2949C56.6851 13.8554 44.3478 1.29492 29.1851 1.29492ZM29.1851 52.6283C16.549 52.6283 6.26839 42.1607 6.26839 29.2949C6.26839 16.4291 16.549 5.96159 29.1851 5.96159C41.8211 5.96159 52.1017 16.4291 52.1017 29.2949C52.1017 42.1607 41.8211 52.6283 29.1851 52.6283Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M17.7267 22.2949H22.31V26.9616H17.7267V22.2949Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M36.0601 22.2949H40.6434V26.9616H36.0601V22.2949Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
</svg>`,
          file: null,
          inProgress: false
        },
        {
          id: 7,
          model: 'disgusted',
          name: 'disgusted',
          icon: `<svg width="48" height="48" viewBox="0 0 58 59" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M29.1962 33.9502C25.2619 33.9502 21.4702 35.4034 18.5184 38.0415L16.7963 39.5802L19.8203 43.087L21.5424 41.5483C23.6573 39.6582 26.3764 38.6169 29.1979 38.6169C32.0211 38.6169 34.734 39.6536 36.8416 41.5369L38.5637 43.0756L41.5878 39.5699L39.8651 38.0295C36.9205 35.3988 33.1322 33.9502 29.1962 33.9502Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M29.1851 1.29492C14.0212 1.29492 1.68506 13.8554 1.68506 29.2949C1.68506 44.7345 14.0212 57.2949 29.1851 57.2949C44.3478 57.2949 56.6851 44.7345 56.6851 29.2949C56.6851 13.8554 44.3478 1.29492 29.1851 1.29492ZM29.1851 52.6283C16.549 52.6283 6.26839 42.1607 6.26839 29.2949C6.26839 16.4291 16.549 5.96159 29.1851 5.96159C41.8211 5.96159 52.1017 16.4291 52.1017 29.2949C52.1017 42.1607 41.8211 52.6283 29.1851 52.6283Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M17.7267 22.2949H22.31V26.9616H17.7267V22.2949Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
<path d="M36.0601 22.2949H40.6434V26.9616H36.0601V22.2949Z" fill="#FDFDFD" stroke="#16132E" stroke-width="2"/>
</svg>`,
          file: null,
          inProgress: false
        },
      ],
      currentModel: 1,
      detectedFaceCounter: [],
      cantDetectFaceError: false,
    }),
    mounted() {
      this.loadModels();
    },
    methods: {
      loadModels() {
        Promise.all([
          faceapi.nets.tinyFaceDetector.loadFromUri('/faceApi_models'),
          faceapi.nets.faceLandmark68Net.loadFromUri('/faceApi_models'),
          faceapi.nets.faceRecognitionNet.loadFromUri('/faceApi_models'),
          faceapi.nets.faceExpressionNet.loadFromUri('/faceApi_models'),
        ]).then(this.startVideo());
        this.setPlayEvent();
      },
      async startVideo() {
        let stream = null;
        const video = document.getElementById('video');
        /* for chrome */
        if (navigator.getUserMedia)
          navigator.getUserMedia(
            {video: {}}, stream => video.srcObject = stream, error => console.log(err)
          );
        /* for firefox */
        else {
          try {
            stream = await navigator.mediaDevices.getUserMedia({video: {}});
            video.srcObject = stream;
          } catch (e) {
            console.log(e)
          }
        }

      },
      setPlayEvent() {
        const video = document.getElementById('video');
        const text = document.getElementById("prediction");
        video.addEventListener('play', () => {

          const canvas = faceapi.createCanvasFromMedia(video);
          document.getElementById('canvas').append(canvas);
          const displaySize = {width: video.videoWidth, height: video.videoHeight};
          faceapi.matchDimensions(canvas, displaySize);
          setInterval(async () => {
            const detection = await faceapi.detectAllFaces(video, new faceapi.TinyFaceDetectorOptions()).withFaceLandmarks().withFaceExpressions()
            const resizedDetections = faceapi.resizeResults(detection, displaySize);
            canvas.getContext('2d').clearRect(0, 0, canvas.width, canvas.height);

            // faceapi.draw.drawDetections(canvas, resizedDetections);
            // faceapi.draw.drawFaceExpressions(canvas, resizedDetections);
            // faceapi.draw.drawFaceLandmarks(canvas, resizedDetections);

            // const predictions = await faceapi.detectAllFaces(video, new faceapi.TinyFaceDetectorOptions()).withFaceLandmarks().withFaceExpressions();
            //
            // const detectionsWithAgeAndGender = await faceapi.detectAllFaces(video, new faceapi.TinyFaceDetectorOptions()).withFaceLandmarks().withAgeAndGender();
            //

            this.checkFaceExpressions(detection[0]);
          }, 100)
        })
      },
      /*  check 2 second face and if in this time face is in one model called take shot function */
      checkFaceExpressions(obj) {
        try {
          /* get face model string*/
          let detectedFaceMode = this.prediction_string(obj);

          this.cantDetectFaceError = !detectedFaceMode;

          for (let i of this.faceModels) {
            if (i.id === this.currentModel && i.file)
              this.currentModel++;
            else if (i.id === this.currentModel && !i.file) {
              i.inProgress = true;
              if (this.detectedFaceCounter.length < 10) {
                if (i.model === detectedFaceMode)
                  this.detectedFaceCounter.push(detectedFaceMode);
                else
                  this.detectedFaceCounter = [];
              }
              if (this.detectedFaceCounter.length === 10) {
                this.takeShot(detectedFaceMode);
                this.detectedFaceCounter = [];
                this.currentModel++;
              }
            }
          }
        } catch (e) {
        }
      },
      prediction_string(obj) {
        let top_prediction = "neutral";
        let maxValue = 0;
        if (!obj) return null;
        for (let p in obj.expressions) {
          if (obj.expressions.hasOwnProperty(p)) {
            maxValue = obj.expressions[p];
            top_prediction = p;
            if (obj.expressions[p].toFixed(2) >= 0.8) {
              top_prediction = p
              return top_prediction
            }
          }
        }
        return top_prediction;
      },
      takeShot(model) {
        if (!model) return;
        const video = document.getElementById('video');
        let canvas = faceapi.createCanvasFromMedia(video);
        for (let i of this.faceModels) {
          if (i.model === model) {
            i.file = canvas.toDataURL('image/jpeg');
            i.inProgress = false;
          }
        }
      },
    }
  }
</script>

<style lang="scss" scoped>

  .camera-frame {
    height: auto;
    width: 100%;
    /*height: auto;*/
    /*width: auto;*/
    position: relative;
    display: flex;
    /*flex-flow: column;*/

    .cameraFrame-video {
      position: relative;
      width: 50%;


      #canvas {
        position: absolute;
      }

      video {
        width: calc(100% - 20px);
        margin: 10px 10px 5px 10px;
        background-color: rgba(0, 0, 0, 0.22);
        border-radius: 5px;
      }

      .frame {
        &:before, &:after {
          content: '';
          position: absolute;
          background-color: rgba(196, 196, 196, 0.7);
          border-radius: 10px;
        }
      }

      .top-left-frame {
        &:before {
          left: 0;
          top: 0;
          width: 70px;
          height: 2.5px;
        }

        &:after {
          left: 0;
          top: 0;
          width: 2.5px;
          height: 70px;
        }
      }

      .top-right-frame {
        &:before {
          right: 0;
          top: 0;
          width: 70px;
          height: 2.5px;
        }

        &:after {
          right: 0;
          top: 0;
          width: 2.5px;
          height: 70px;
        }
      }

      .bottom-left-frame {
        &:before {
          left: 0;
          bottom: 0;
          width: 70px;
          height: 2.5px;
        }

        &:after {
          left: 0;
          bottom: 0;
          width: 2.5px;
          height: 70px;
        }
      }

      .bottom-right-frame {
        &:before {
          right: 0;
          bottom: 0;
          width: 70px;
          height: 2.5px;
        }

        &:after {
          right: 0;
          bottom: 0;
          width: 2.5px;
          height: 70px;
        }
      }
    }

    .cameraFrame-images {
      width: 50%;
      height: 100%;
      padding: 10px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: space-between;

      .cameraFrame-images-item {
        display: flex;
        align-items: flex-start;
        justify-content: space-between;
        width: 100%;
        height: 100px;
        /*background-color: #4caf50;*/
        .images-item-emoji {
          width: 70px;
          height: 70px;
          border-radius: 50px;
          border: 2px dashed #3C4858;
          padding: 5px;
          display: flex;
          align-items: center;
          justify-content: center;

          svg {
            width: 48px;
            height: 48px;
          }
        }

        .images-item-description {
          padding-right: 10px;

          span {
            font-weight: bold;
            font-size: 15px;
          }

          p {
            opacity: 0.6;
          }
        }

        .images-progress {
          background-color: rgba(#27ae60, 0.2);
          height: 35px;
          width: 70px;
          border-radius: 50px;
          display: flex;
          align-items: center;
          justify-content: center;

          img {
            width: 100%;
            height: auto;
          }
        }

      }

      .cameraFrame-images-gallery {
        width: 100%;
        height: auto;
        display: flex;
        justify-content: space-around;
        flex-flow: wrap;
        margin-top: 30px;

        .gallery-item {
          position: relative;
          width: 150px;
          height: auto;
          overflow: hidden;
          margin: 10px;

          .images-item-emoji {
            position: absolute;
            top: 0;
            right: 0;

            svg {
              width: 25px;
              height: 25px;
            }
          }

          img {
            border-radius: 10px;
            width: 100%;
            height: auto;
          }
        }

      }
    }


  }
  .detectError {
    padding: 5px 10px;
    background-color: rgba(233, 30, 99, 0.52);
  }
</style>
