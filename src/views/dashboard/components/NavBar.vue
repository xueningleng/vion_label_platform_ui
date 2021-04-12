<template>
    
</template>

<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
    
})
</script>

<style lang="scss" scoped>
$major_color1: #fbe6bc;
$major_color2: #d1b8c5;
$major_color3: #b6cbd6;
$major_color4: #667286;

.navbar{
    float: left;
    width: 40px;
    min-height: 140px;
    .pane{
        .title-container{
            position: relative;
            background-color: $major_color4;
            border-style: "double";
            border-color: $major_color3;
            .title{
                font-family: 微软雅黑;
                font-size: 16px;
                color:$major_color1;
                text-align: center;
                font-weight: bold;
            }
        }
    }
}
</style>