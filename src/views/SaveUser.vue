<template>
  <a-form :layout="formState" :model="formState">
    <a-form-item label="姓名">
      <a-input v-model:value="formState.username" placeholder="请添加姓名" />
    </a-form-item>
    <a-form-item label="性别">
      <a-select
        v-model:value="formState.sex"
        style="width: 120px"
        :options="options"
      >
    </a-select>
    </a-form-item>
    <a-form-item label="年龄">
      <a-input-number v-model:value="formState.age" placeholder="请添加年龄" />
    </a-form-item>
    <a-form-item >
      <a-button type="primary" @click="saveOrUpdate">保存/更新</a-button>
    </a-form-item>
  </a-form>
</template>
<script lang="ts" setup>
    import axios from 'axios';
    import { ref } from 'vue';
    import { message } from 'ant-design-vue';
    import { useRouter } from 'vue-router';
    import type { SelectProps } from 'ant-design-vue';

    interface FormState {
      id?:number
      username: string;
      sex: string;
      age: number;
      flag?:string;
    }

    const options = ref<SelectProps['options']>([
      { value: '男', label: '男' },
      { value: '女', label: '女' }
    ]);

    const formState = ref<FormState>({
      username: '',
      sex: '男',
      age: 1,
      flag:'save'
    });
    const router = useRouter()
    //获取路由的参数
    let p = history.state.person
    
    if(p){
      //转对象
      formState.value = JSON.parse(p)
      console.log('------------>>',formState.value);
    }
   //更新
    const updateUser=()=>{
      formState.value.flag='update'
      axios.put('/api/tuser/update',formState.value)
      .then(()=>{
        message.success('更新成功')
        router.push('/showUser')
      })      
    }
    
    //保存
    const saveUser=()=>{
      axios.post('/api/tuser/save',formState.value)
      .then(()=>{
        message.success('保存成功')
        router.push('/showUser')
      })      
    }

    const saveOrUpdate = () => {
      if(formState.value.id){
        updateUser()
      }else{
        saveUser()
      }
    };

</script>

