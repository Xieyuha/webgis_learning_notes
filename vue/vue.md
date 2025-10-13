#vue3简介
#创建vue工程
##基于vite创建vue3工程
vite项目中，index.html是项目的入口文件，在项目最外层
加载index.html后，vite解析<script type="module" src="/src/main.ts"></script>，指向JavaScript
vue3中是通过createApp函数创建一个应用实例