<template>
  <div class="container-fluid">
    <div class="row">
      <div class="col-12 d-flex align-items-center justify-content-center vh-100">
        <FaceDetection></FaceDetection>
      </div>
    </div>
  </div>
</template>

<script>
  import faceDetection from "@/components/faceDetection/index";
export default {
  components:{faceDetection}

}
</script>

<style>

</style>
