<!-- Coded with ♥ by Yago Estévez -->

<template>
  <main id="app">
    <transition name="fade">
      <div v-if="loaded" id="frame" :style="{ 'background-image': 'url(' + this.currImg + ')' }">
        <div id="quote-box" class="container">
          <span class="doubleQuotes">"</span>
          <div class="quotesDisplay">
            <h1 class="title">Quote of the Day</h1>
            <p id="text" class="quote" v-text="this.currQuote"></p>
            <span id="author" class="author" v-text="this.currAuthor"></span>
            <Buttons :newQuote="newQuote" :currQuote="currQuote" :currAuthor="currAuthor" />
          </div>
        </div>
        <Footer />
      </div>
    </transition>
  </main>
</template>

<script>
import Buttons from './components/Buttons';
import Footer  from './components/Footer';

export default {
  name: "App",
  components: {
    Buttons, Footer
  },
  data() {
    return {
      loaded    : false,
      currQuote : null,
      currAuthor: null,
      currImg   : null
    }
  },
  methods: {
    newQuote() {
      this.loaded = false;
      const quoteURL = "https://andruxnet-random-famous-quotes.p.mashape.com/?cat=famous&count=1";
      const params   = {
        method  : 'GET', 
        mode    : 'cors', 
        redirect: 'follow',
        headers : new Headers( {
          "X-Mashape-Key" : "tJlXFg8USLmshxAaBnhQxOROSXEap14KI4jjsnt378uAEshDYN",
          "Accept"        : "application/json"
        } )
      };

      fetch( quoteURL, params )
        .then( response => {
          const contentType = response.headers.get( "content-type" );
          if ( contentType && contentType.indexOf( "application/json" ) !== -1 ) {
            response.json()
              .then( json => {
                this.currQuote = json[0].quote;
                this.currAuthor = json[0].author;
                this.loadPicture( this.currAuthor );
              } )
          } else {
            console.error( "Oops, we haven't got the data from the quotes API!" );
          }
        } )
    },
    loadPicture() {
      const wikiURL  = "https://en.wikipedia.org/w/api.php?action=query&formatversion=2&prop=pageimages&format=json&origin=*&titles=";
      const params = {
        method  : 'GET', 
        mode    : 'cors', 
        redirect: 'follow'
      };

      fetch( wikiURL + this.currAuthor, params )
        .then( response => {
          const contentType = response.headers.get( "content-type" );
          if ( contentType && contentType.indexOf( "application/json" ) !== -1 ) {
            response.json()
              .then( json => {
                if ( !json.query.pages[ 0 ].thumbnail ) {
                  const bgImgURL   = "https://www.dropbox.com/s/q4f9h5xxdlesen4/quote.jpg?raw=1";
                  this.currImg     = new Image( );
                  this.currImg.src = bgImgURL;
                  this.currImg.onload = ( ) => {
                    this.loaded = true;
                    this.currImg = bgImgURL;
                  };
                } else {
                  const originalImgURL = json.query.pages[ 0 ].thumbnail.source;
                  const resizedImgURL  = originalImgURL.replace( /([ 0-9 ]+)px/i , '500px' );
                  const preloadImg     = new Image( );
                  preloadImg.src       = resizedImgURL;
                  preloadImg.onload    = ( ) => {
                    this.loaded = true;
                    this.currImg = resizedImgURL;
                  };
                }
              } )
          } else {
            console.error( "Oops, we haven't got the data from Wikipedia!" );
          }
        } );
    }
  },
  beforeMount() {
    this.newQuote( );
  }
};
</script>

<style>
  #frame {
    position                  : absolute;
    max-width                 : 1000px;
    max-height                : 800px;
    width                     : 70vw;
    height                    : 70vh;
    top                       : 50%;
    left                      : 50%;
    -webkit-transform         : translate(-50%, -50%);
    transform                 : translate(-50%, -50%);
    background-image          : url(https://www.dropbox.com/s/q4f9h5xxdlesen4/quote.jpg?raw=1);
    background-size           : cover;
    background-position       : center;
    border-radius             : 5px;
    border                    : #f5f5dc 10px solid;
    -webkit-box-shadow        : -2px 2px 20px #11111170;
    box-shadow                : -2px 2px 20px #11111170;
  }

    .container {
      background-color        : #44444420;
      height                  : 100%;
      padding                 : 5vh 5vw;
      border-radius           : 10px;
      overflow                : hidden;
    }

    .doubleQuotes {
      position                : absolute;
      top                     : -50px;
      left                    : 10px;
      font-size               : 18em;
      font-family             : Coustard, serif;
      color                   : #f5f5dc90;
      z-index                 : -1;
    }

      .quotesDisplay {
        display               : -webkit-box;
        display               : -ms-flexbox;
        display               : flex;
        -webkit-box-orient    : vertical;
        -webkit-box-direction : normal;
        -ms-flex-direction    : column;
        flex-direction        : column;
        height                : 100%;
        width                 : 100%;
        font-size             : 1.6em;
        text-align            : center;
      }

        h1.title {
          -ms-flex-negative   : 1;
          flex-shrink         : 1;
          -webkit-box-flex    : 0;
          -ms-flex-positive   : 0;
          flex-grow           : 0;
          font-size           : 1em;
          text-align          : left;
          text-decoration     : underline;
          text-shadow         : 3px 3px 5px #000;
        }
        
        .quote {
          -webkit-box-flex    : 1;
          -ms-flex            : 1;
          flex                : 1;
          display             : -webkit-box;
          display             : -ms-flexbox;
          display             : flex;
          -webkit-box-align   : center;
          -ms-flex-align      : center;
          align-items         : center;
          text-shadow         : 3px 3px 5px #000;
          font-size           : 1em;
          text-align          : center;
        }

        .author {
          -ms-flex-negative   : 1;
          flex-shrink         : 1;
          -webkit-box-flex    : 0;
          -ms-flex-positive   : 0;
          flex-grow           : 0;
          padding-bottom      : 1em;
          font-size           : 0.7em;
          text-shadow         : 3px 3px 5px #000;
        }

  @media (max-width: 700px), handheld {
    body {
      font-size               : 12px;
      line-height             : 1.1;
    }
      main.bgImage {
        width                 : 70vw;
        height                : 70vh;
        min-height            : 400px;
      }
        div > .doubleQuotes {
          font-size           : 12em;
          position            : absolute;
          top                 : 0px;
          left                : 10px;
        }
  }
</style>
