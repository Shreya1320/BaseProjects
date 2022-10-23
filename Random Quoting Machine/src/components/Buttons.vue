<template>
  <div class="actions">
    <a id="tweet-quote"
      aria-hidden="true"
      title="Share on Twitter!"
      :href="`https://twitter.com/intent/tweet?hashtags=QuoteMachine,Quotes,FreeCodeCamp,Coders,Dev,Vue&via=YagoEstevez&related=freecodecamp&text=${encodeURIComponent( this.currQuote + ' —' + this.currAuthor + '\n' )}`"
      target="_blank"
      rel="noopener noreferrer"
    ><i class="twitterBtn fa fa-twitter"></i></a>
    <i id="new-quote"
      class="newQuoteBtn fa fa-random"
      aria-hidden="true"
      title="Get a new random quote!"
      @click="newQuote"
    ></i>
  </div>
</template>

<script>
export default {
  name: 'Buttons',
  props: {
    newQuote  : Function,
    currQuote : String,
    currAuthor: String
  }
}
</script>

<style scoped>
  div.actions {
    -ms-flex-negative   : 1;
    flex-shrink         : 1;
    -webkit-box-flex    : 0;
    -ms-flex-positive   : 0;
    flex-grow           : 0;
    display             : -webkit-box;
    display             : -ms-flexbox;
    display             : flex;
    -ms-flex-pack       : distribute;
    justify-content     : space-around;
    font-size           : 1em;
    padding-top         : 1.5em;
  }

    a {
      outline           : none;
    }

    i {
      cursor            : pointer;
      border-radius     : 100%;
      padding           : 0.5em;
      color             : #f5f5dc;
      transition        : 300ms; 
    }

    .twitterBtn, .newQuoteBtn {
      border            : #f5f5dc 2px solid;
    }

    i:hover {
      color             : #d33333;
      background-color  : #f5f5dc;
    }
</style>
