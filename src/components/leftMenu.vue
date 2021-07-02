<template>
    <el-menu>
        <el-menu-item index="2">
            <i class="el-icon-menu"></i>
            <span slot="title"><a href="#duty">排班管理</a></span>
        </el-menu-item>
        <el-menu-item index="3">
            <i class="el-icon-document"></i>
            <span slot="title"><a href="#process">进程管理</a></span>

        </el-menu-item>
        <el-menu-item index="4">
            <i class="el-icon-setting"></i>
            <span slot="title"><a href="#course">课表管理</a></span>
        </el-menu-item>
    </el-menu>
</template>

<script>
export default {
    name: "leftMenu"
}
</script>

<style scoped>

</style>
