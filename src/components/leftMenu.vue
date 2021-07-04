<template>
    <div>
        <el-menu>
            <a href="#duty">
                <el-menu-item index="2">
                    <i class="el-icon-menu"></i>
                    <span slot="title">排班管理</span>
                </el-menu-item>
            </a>
            <a href="#process">
                <el-menu-item index="3">
                    <i class="el-icon-document"></i>
                    <span slot="title">进程管理</span>

                </el-menu-item>
            </a>
            <a href="#course">
                <el-menu-item index="4">
                    <i class="el-icon-setting"></i>
                    <span slot="title">课表管理</span>
                </el-menu-item>
            </a>
        </el-menu>
        <div>
            <div style="font-size: small; font-family: monospace; position: absolute; bottom: 0; margin: 10px;">
                <el-divider></el-divider>
                <div style="font-size: large; font-weight: bold;">Info</div>
                FrontEnd: Vue2, ElementUI<br>
                BackEnd: Vert.X Web(Java)<br>
                Author: zsh2517<br>
                1190200612-张世豪<br>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "leftMenu"
}
</script>

<style scoped>

</style>
