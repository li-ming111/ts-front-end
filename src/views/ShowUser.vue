<template>
  <div>
    <a-space>
      <a-input v-model:value="searchUsername" placeholder="请输入姓名" 
      style="width: 200px; margin-bottom: 8px;" />
      <a-input-number v-model:value="searchAge" placeholder="请输入年龄" 
      style="width: 200px; margin-bottom: 8px;" />
      <a-button type="primary" @click="onSearch">搜索</a-button>
    </a-space>
  </div>
  <a-table bordered :data-source="dataSource" :columns="columns" 
     :pagination="pagination" @change="onChange">    
    <template #operation="{ record }">
      <a-Space>
      <a-button type="primary" @click="onEdit(record)">编辑</a-button>
      <a-popconfirm
        v-if="dataSource.length"
        title="是否删除?"
        cancelText="取消"
        okText="确定"
        @confirm="onDelete(record.id)"
      >
        <a-button type="primary" danger>删除</a-button>
      </a-popconfirm>
      </a-Space>
    </template>
  </a-table>
</template>
<script lang="ts" setup>
import axios from 'axios';
import { onMounted, ref } from 'vue';
import { message } from 'ant-design-vue';
import { useRouter } from 'vue-router';

interface DataItem {
  id: number;
  username: string;
  age: number;
  sex: string;
}

const searchUsername = ref('');
const searchAge = ref(0);

const router = useRouter()

//编辑
const onEdit=(record:DataItem)=>{
    router.push({
        path:'/addUser',
        state:{
          person:JSON.stringify(record)
        }
    })  
}

const columns = [
    {
    title: '姓名',
    dataIndex: 'username',
    width: '30%'
    },
    {
    title: '性别',
    dataIndex: 'sex',
    },
    {
    title: '年龄',
    dataIndex: 'age',
    },
    {
    title: '操作',
    dataIndex: 'operation',
    slots: { customRender: 'operation' },
    },
];
//数据源
const dataSource = ref([]);
//----------分页-------------   
const pagination = ref({
	total:0,
	current:1,
	pageSize:3,
	showr:true,
	pageSizeOptions:['3','10','15'],
	showQuickJumper:true,
	showSizeChanger:true,
	onShowSizeChange:pageSizeChange,
	onChange:pageChange,
 })

 //页码变化
 function pageSizeChange(current:number,pageSize:number){
	pagination.value.current = current
	pagination.value.pageSize = pageSize
 }
 //点击下一页
 function pageChange(page:number, pageSize:number){
   pagination.value.current = page
   pagination.value.pageSize = pageSize
 }

 //当前页码变化
 const onChange = (pag:any) => {	
   pagination.value.current = pag.current
   pagination.value.pageSize = pag.pageSize
   show()
};
 //-------------------------------------
//获取数据
const show=()=>{
   axios.post('/api/tuser/list1',
   {current:pagination.value.current,size:pagination.value.pageSize,
    username:searchUsername.value,age:searchAge.value})
    .then(res=>{
        dataSource.value=res.data.data.list;
        pagination.value.total = res.data.data.total
    })
    .catch(err=>{
        console.log(err);
    })
}

onMounted(()=>{
    show();    
})

//删除
const onDelete=(id:number)=>{
    axios.delete(`/api/tuser/delete/${id}`)
    .then(()=>{       
        show();
        message.success('删除成功');
    })
    .catch(err=>{
        console.log(err);
    })
} 
//搜索
const onSearch=()=>{
    show();
}  
</script>
<style lang="less">
.editable-cell {
  position: relative;
  .editable-cell-input-wrapper,
  .editable-cell-text-wrapper {
    padding-right: 24px;
  }

  .editable-cell-text-wrapper {
    padding: 5px 24px 5px 5px;
  }

  .editable-cell-icon,
  .editable-cell-icon-check {
    position: absolute;
    right: 0;
    width: 20px;
    cursor: pointer;
  }

  .editable-cell-icon {
    margin-top: 4px;
    display: none;
  }

  .editable-cell-icon-check {
    line-height: 28px;
  }

  .editable-cell-icon:hover,
  .editable-cell-icon-check:hover {
    color: #108ee9;
  }

  .editable-add-btn {
    margin-bottom: 8px;
  }
}
.editable-cell:hover .editable-cell-icon {
  display: inline-block;
}
</style>
