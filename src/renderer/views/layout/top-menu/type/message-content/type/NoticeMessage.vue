<template>
  <ul style="width:98%; height:100%; margin:0 auto; overflow:auto;">
    <li v-for="n in 18" class="box-shadow" style="width:98%; padding-right:8px; box-sizing:border-box;">
      <div style="width:100%; height:100%; padding-left:50px; position:relative; box-sizing:border-box;">
        <div style="width:48px; height:100%; top:0; left:0; position:absolute;">
          <page-link url="/user" style="width:33px; height:33px; margin:7px; float:left; border-radius:50%;">
            <img class="box-show" src="http://p2.music.126.net/kaISxJU3yP0Qvw6H_vUyAQ==/18984167765401316.jpg?param=80y80" style="width:100%; height:100%; padding:2px; box-sizing:border-box; border-radius:50%;" />
          </page-link>
        </div>
        <div style="padding:3px 0 10px;">
          <p style="line-height:1.9em;">
            <page-link url="/user">Music House</page-link>
            <span style="margin-top:8px; float:right; font-size:13px; color:#999; line-height:1em;">2018-09-12</span>
          </p>
          <p style="margin-top:-3px; word-break:break-word; line-height:1.2em; font-size:13px;">
            填充的文字填充的文字填充的文字填充的文字填充的文字
          </p>
        </div>
      </div>
    </li>
  </ul>
</template>

<script>
  export default {
    name: 'NoticeMessage'
  }
</script>

<style scoped>

</style>