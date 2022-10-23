<template>
  <footer>coded with
    <i class="love fa fa-heart" aria-hidden="true"></i> by
    <a href="https://twitter.com/yagoestevez" class="link">Yago Estévez</a>
  </footer>
</template>

<script>
export default {
  name: 'Footer'
}
</script>

<style scoped>
  footer {
    font-size               : 1rem;
    color                   : #f5f5dc;
    text-align              : center;
    padding-top             : 1.5rem;
  }

    .love {
      color                 : #d33333;
    }

    footer a:link,
    footer a:visited {
      text-decoration       : none;
      display               : inline-block;
      border-radius         : 100px;
      -webkit-transition    : 300ms;
      transition            : 300ms;
      position              : relative;
      color                 : #f5f5dc;
    }

    footer a:hover {
      color                 : #d33333;
      text-shadow           : 0px 1px 5px #111;
    }
</style>
