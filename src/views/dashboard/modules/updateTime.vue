<template>
    <a-modal title="修改日期" :visible="visible" @ok="updateOk" @cancel="updateCancel" :width="300">
        <a-date-picker @change="updateDate" style="width:100%;"/>
    </a-modal>

   
</template>
<script>
export default {
    name:'updateTime',
    data(){
        return{
            visible:false,
            
        }
    },
   
    methods:{
        show(){
            this.visible = true;
        },
        updateOk(){
            this.visible = false;
        },
        updateCancel(){
            this.visible = false;
        },
        updateDate(date,dateString){
            console.log(date,dateString);
        },
        
    }    
}
</script>
<style lang="less" scoped>
   
</style>
