<template>
  <center>
    <div id='xmlEditorWrapper'>
        <div class="image-upload" style="text-align:right;">
          <label style="font-weight:bold; color:#FEF2EF; font-size:1.5em">
            Open the file you want to edit
          </label>
          <label for="file-input">
            <i class="fa-solid fa-folder-open" id='file-input-button'></i>
          </label>
          <input id="file-input" type="file" @change="fileupload" accept=".xml, .smd"/>
        </div>
        <!-- <input id='inputfile' type='file' @change="fileupload" accept=".xml, .smd"/> -->
        <!-- <input id='inputfile' type='file' @change="fileupload" accept=".xml"/> -->
        <!-- <button v-on:click='resetEditor'>reset</button> -->
        <TreeVue v-on:event-save='saveXml'
            v-on:event-okconfirm='okConfirm'/>
    </div>
  </center>
</template>

<script>
// import XMLFileReader from '../store/XmlFileReader.js'
// import xmlPretty from '../store/XmlPretty.js'
// import xmlParser from '../store/XmlParser.js'
import { xmlFilereader, xmlParser, xmlValidate, xmlBuild } from '../store/XmlHelper';
import TreeVue from './TreeVue.vue';
import Swal from 'sweetalert2';
let target = undefined;
let xml = undefined;
let tree;

export default {
    name: 'xmlEditor',
    mounted() {
      tree = document.getElementById('tree')
      document.body.addEventListener('contextmenu', e => {
        e.preventDefault();
      });
    },
    components: {
      //ContextMenu,
      TreeVue
    },
    methods: {
      'fileupload': async function(event){
          let eventarget = event.target || event.srcElement;
          
          if (eventarget.value.length == 0) {
            return;
          }
          target = (event.target.files)[0];
          let readedfile = await xmlFilereader(target);
          let validate = xmlValidate(readedfile);
          if(validate !== true){
            this.xmlError(validate);
            return;
          }
          xml = xmlParser(readedfile);
          this.makeTree(xml);
        },
        'makeTree': function(xml){
          tree.innerHTML = '';
          TreeVue.methods.initTree(xml);
        },
        'xmlError': function(validate){
          let errmsg = '<div style="text-align:left">code: ' + validate.err.code + '<br/>col: ' + validate.err.col + '<br/>line: ' + validate.err.line + '<br/>msg: ' + validate.err.msg + "</div>";
          Swal.fire({
            title: 'Error!', html: errmsg, icon: 'error', confirmButtonText: 'OK'
          });
        },
        'saveXml': function(xmldata){
          if(xmldata === undefined) return;

          let strxml = xmlBuild(xmldata);
          console.log(strxml);
          let validate = xmlValidate(strxml);

          if(validate !== true){
            this.xmlError(validate);
            return;
          }
          
          // console.log(strxml);
        },
        'resetEditor':function(){
          document.getElementById('inputfile').value = null;
          target = undefined;
          xml = undefined;
          tree.innerHTML = '';
        },
        'okConfirm':function(_title, msg, level){
          Swal.fire({
            title: _title + '!', 
            text: msg, 
            icon: level, 
            confirmButtonText: 'OK'
          });
        },
    },
}
</script>

<style>
/* @import url(https://fonts.googleapis.com/css?family=Open+Sans:300,700,300italic);
*, *:before, *:after {
  -webkit-box-sizing: border-box;
          box-sizing: border-box;
} */

#xmlEditorWrapper{
  width:32rem;
  /* height:24rem; */
  text-align: left;
  margin: 0px; padding: 0px;
  -webkit-user-select:none; -moz-user-select:none; -ms-user-select:none; user-select:none;
}

/* #inputfile {
  width:40%;
  height: 1.2rem;
  border: 0.1rem solid #ccc;
  border-radius: 0.2rem;
  padding: 0.1rem 0.2rem;
  text-align: left;
  background-color: cornsilk;
}
#inputfile::file-selector-button{
  display: none;
} */

.image-upload>input {
  display: none;
}

#file-input-button{
    color: #FFD16B;
    text-shadow: 1px 1px 1px #ccc;
    font-size: 1.5em;
}

</style>