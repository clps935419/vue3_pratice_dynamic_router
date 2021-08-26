<script>
import { onMounted, onUnmounted, reactive, ref } from '@vue/runtime-core';
import {useRoute,useRouter} from 'vue-router';
import axios from "axios";
export default {
  setup() {
    const route = useRoute();
    const router = useRouter();
    const courses = reactive({
      data:{}
    })
    const isError = ref(false);
    let timer = null;
    onMounted(()=>{
      axios
        .get(`https://vue-lessons-api.herokuapp.com/courses/${route.params.id}`)
        .then((res) => {
          console.log('----res',res)
          courses.data = res.data.data[0];
          console.log('----res',courses.data)
        })
        .catch((error)=>{
          console.log('error',error.response)
          isError.value = true;
          courses['errorMsg'] = error.response.data.error_message;
          //3秒後回去首頁的功能
          timer = setTimeout(() => {
            //回去指定頁面
            router.push({
              path:'/Courses'
            });
            //回去上一頁
            // router.go(-1);
          }, 3000);
        })
        ;
    });
    //如果離開畫面要清除setimeout，否則會執行
    onUnmounted(()=>{
      clearTimeout(timer);
    })
    return {
      courses,
      isError
    };
  },
};
</script>
<template>
  <div>
    <div v-if="!isError">
      <h1>{{courses.data.name}}</h1>
      <h2>NTD:{{courses.data.money}}</h2>
      <img :src="courses.data.photo" alt="" />
      <div v-if="Object.keys(courses.data).length!==0">
        <img :src="courses.data.teacher.img" alt="" />
        <p>{{courses.data.teacher.name}}</p>
      </div>
    </div>
    <!-- error_message -->
    <h1 v-if="isError">
      {{courses.errorMsg}}
    </h1>
  </div>
</template>

<style></style>
