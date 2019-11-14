<template>
    <v-app> 
    <div>
        <v-toolbar app dark color="purple darken-4 pink--text text--darken-1">
        <v-toolbar-title class="headline text-uppercase">
        <span>EMOJIFY &nbsp;</span>
        <span class="font-weight-light white--text">FRIENDLY Facial expression detection</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn
        color = "grey darken-4"
        flata
        href="https://github.com/niaz2dn/emojify"
        target="_blank"
      >
        <span class="mr-2">Latest Release</span>
      </v-btn>
    </v-toolbar>
        <v-col>
        <v-spacer></v-spacer>
        </v-col>
        <div class="box">
            <div class = "row">
            <img ref="im" v-if="previewImage" :src="previewImage">
            <canvas ref="myCanvas" width = "400" height = "300"/>

            </div>
        </div>
        <v-col>
        <v-spacer></v-spacer>
        </v-col>
       <div class="text-center">
          <v-btn
            max-width="320"
            color="light-blue"
            class="ma-2 white--text">
            <input type="file" ref= "myFileName" @change= uploadImage>
              <v-dialog v-if="variable === false" v-model="dialog2" max-width="290">
                <v-card>
                  <v-card-title class="headline"> Upload .jpg, .png or .jpeg files only </v-card-title>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn
                      color="green darken-1"
                      text
                      @click= reset
                      @click.stop="dialog2 = false"
                    >
                      Ok
                    </v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
            <v-icon right dark>mdi-cloud-upload</v-icon>
          </v-btn>
        <v-btn
            color="purple"
            class="ma-2 white--text"
            @click = detect(specificTitle,previewImage)>
            Detect
        </v-btn>
    <v-col>
        <v-spacer></v-spacer>
    </v-col>
    <v-spacer></v-spacer>
     <v-row justify="center">
    <v-btn text icon color="green"
    @click.stop="dialog = true">
        <v-icon>mdi-cached</v-icon>
      Reset
    </v-btn>
    <v-dialog v-model="dialog" max-width="290">
      <v-card>
        <v-card-title class="headline">Are you sure you want to reset?</v-card-title>

        <v-card-actions>
          <v-spacer></v-spacer>

          <v-btn
            color="green darken-1"
            text
            @click= reset
            @click.stop="dialog = false"
          >
            Yes
          </v-btn>

          <v-btn
            color="green darken-1"
            text
            @click="dialog = false"
          >
            No
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
        <img class = "hidden" ref="angry" src = "../assets/angry.png" alt = "angry emoji" />
        <img class = "hidden" ref="calm" src = "../assets/calm.png" alt = "calm emoji" />
        <img class = "hidden" ref="confused" src = "../assets/confused.png" alt = "confused emoji" />
        <img class = "hidden" ref="disgusted" src = "../assets/disgusted.png" alt = "disgusted emoji" />
        <img class = "hidden" ref="fear" src = "../assets/fear.png" alt = "fear emoji" />
        <img class = "hidden" ref="happy" src = "../assets/happy.png" alt = "happy emoji" />
        <img class = "hidden" ref="sad" src = "../assets/sad.png" alt = "sad emoji" />
        <img class = "hidden" ref="surprised" src = "../assets/surprised.png" alt = "surprised emoji" />
        <img class = "hidden" ref="logo" src = "../assets/logo.png" alt = "vuejs logo" />
        </div>
    </div>
    <v-btn
    color = "indigo"
    text
    @click= setListImages();
    >List Images
    </v-btn>
      <v-card
    width="500"
    class="mx-auto"> 
    <v-toolbar color="indigo" dark v-if="showListVar">
      <v-toolbar-title>List Images</v-toolbar-title>
      <v-spacer></v-spacer>
    </v-toolbar>
    <v-list v-if="showListVar">
      <v-list-item v-for="item in items" :key="item.title" @click="getSpecific(item.title)">
        <v-list-item-content> 
          <v-list-item-title v-text="item.title"></v-list-item-title>
        </v-list-item-content>
        <v-list-item-avatar >
          <img :src="item.avatar"/>
        </v-list-item-avatar>
      </v-list-item>
    </v-list>
  </v-card>
     </v-app>
</template>
<script>
import axios from 'axios';

// Used to manipulate the image and add bounding boxes
// See: https://github.com/lovell/sharp
//const sharp = require('sharp');

export default {
    name:'imageUpload',
    data(){
        return{
            previewImage:null,
            file:null,
            data:null,
            dialog: false,
            variable: true,
            dialog2:false,
            data64:"",
            boolGet:false,
            listImages:[],
            items: [],
            showListVar:false,
            booleanPutList:true,
            specificBool:true,
            specificTitle:"",
          }
        },

    methods:{
        getImage(nomImage){
         return axios.get('https://hw7frdmkj6.execute-api.eu-west-2.amazonaws.com/prod/image/?fileName='+ nomImage, ).then(response => {
                return response.data.body;
              })
        },
        getSpecific(nomImage){
          this.specificTitle=nomImage;
          this.specificBool=true;
          if(this.specificBool){
          axios.get('https://hw7frdmkj6.execute-api.eu-west-2.amazonaws.com/prod/image/?fileName='+ nomImage, ).then(response => {
                this.previewImage=response.data.body;
              })
          }
        },
        setListImages(){
            this.showListVar=!this.showListVar;
            if(this.booleanPutList){
            axios.get('https://hw7frdmkj6.execute-api.eu-west-2.amazonaws.com/prod/listimages', ).then(response => {
            this.listImages=response.data.body;
            for(var i=0;i<this.listImages.length;i++){
              let titre = this.listImages[i];
              this.getImage(titre).then( res => {
              let jsonimg={ title: titre, avatar: res}
              this.items.push(jsonimg);
              this.booleanPutList=false;
              })

              
          }
            })
            }
        },
        reset(){ 
            this.previewImage = null;
            this.file=null;
            this.$refs['myCanvas'].width = 400;
            this.$refs['myCanvas'].height = 300;
            this.$refs['myFileName'].value = null;
            this.specificBool=false;     
            this.showListVar=false;    
        },
        uploadImage(e){
            const image = e.target.files[0];
            this.file = e.target.files[0];
            const reader = new FileReader();

           let ext= this.file.name.split('.')[1];
           if (ext != 'jpeg' && ext != 'jpg' && ext != 'png'){
             this.variable=false;
             this.dialog2=true;
           }
           else{
             this.variable=true;
           }
           console.log(this.variable);
           
           console.log(ext);
            reader.readAsDataURL(image);
            reader.onload = e =>{
                this.previewImage = e.target.result;
                this.data = (reader.result).split(',')[1];
            }
            console.log(this.file.name);
        },
        detect(name,body){
            this.$refs['myCanvas'].width = this.$refs['im'].clientWidth;
            this.$refs['myCanvas'].height = this.$refs['im'].clientHeight;
            
            if(name && body) {
              axios.post('https://hw7frdmkj6.execute-api.eu-west-2.amazonaws.com/prod/detect', 
            {
                "fileName": name,
                "body": body.split(',')[1],
            })
            .then(response => {
                var c = this.$refs['myCanvas']
                var ctx = c.getContext("2d");
                var img = this.$refs['im']

                //draw original image
                ctx.drawImage(img, 0, 0, c.width, c.height);
                let faceDetail = response.data.body.FaceDetails;
                faceDetail.forEach(box => {
                    let left = this.$refs['myCanvas'].clientWidth * box.BoundingBox.Left
                    let top = this.$refs['myCanvas'].clientHeight * box.BoundingBox.Top
                    let width = this.$refs['myCanvas'].clientWidth * box.BoundingBox.Width
                    let height = this.$refs['myCanvas'].clientHeight * box.BoundingBox.Height
                    let emotions = box.Emotions;
                    let emoj = emotions.reduce((prev, current) => (prev.Confidence > current.Confidence) ? prev : current)
                    emoj = emoj.Type.toLowerCase();
                    if(emoj == null) {
                        emoj='logo';
                    }
                    var imag = new Image();
                    imag.src = this.$refs[emoj].src;
                    ctx.drawImage(imag, left, top, width, height);
                });
            })
            .catch(error => console.log(error));

            }
            else {
            axios.post('https://hw7frdmkj6.execute-api.eu-west-2.amazonaws.com/prod/detect', 
            {
                "fileName": this.file.name,
                "body": this.data,
            })
            .then(response => {
                var c = this.$refs['myCanvas']
                var ctx = c.getContext("2d");
                var img = this.$refs['im']

                //draw original image
                ctx.drawImage(img, 0, 0, c.width, c.height);
                let faceDetail = response.data.body.FaceDetails;
                faceDetail.forEach(box => {
                    let left = this.$refs['myCanvas'].clientWidth * box.BoundingBox.Left
                    let top = this.$refs['myCanvas'].clientHeight * box.BoundingBox.Top
                    let width = this.$refs['myCanvas'].clientWidth * box.BoundingBox.Width
                    let height = this.$refs['myCanvas'].clientHeight * box.BoundingBox.Height
                    let emotions = box.Emotions;
                    let emoj = emotions.reduce((prev, current) => (prev.Confidence > current.Confidence) ? prev : current)
                    emoj = emoj.Type.toLowerCase();
                    if(emoj == null) {
                        emoj='logo';
                    }
                    var imag = new Image();
                    imag.src = this.$refs[emoj].src;
                    ctx.drawImage(imag, left, top, width, height);
                });
            })
            .catch(error => console.log(error));
            }
        },

    }
}
</script>



<style>
    .uploading-image{
        display:flex;
        flex-direction: row;
   }

   canvas{
       border:3px solid #d3d3d3;

   }

    .bouton{
        display:flex;
        flex-direction: row;
   }

    .box{
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    h3{
        color: white;
    }

    .hidden{
        display: none;
    }
 </style>