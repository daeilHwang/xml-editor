<template>
  <div>
    <XmlEditor/>
  </div>
</template>

<script>
import XmlEditor from './components/XmlEditor.vue'

export default {
  name: 'App',
  components: {
    XmlEditor
  },
}
</script>

<style>

html{
  width: 100%;
  height: 100%;
}

body {
  font-family: 'Open Sans', sans-serif;
  font-size: 12px;
  background: #011625;
  /* background: #d0e6ef; */
  color: #666;
  width: 100%;
  height: 100%;
  margin: 0px; padding: 0px;
}

</style>
