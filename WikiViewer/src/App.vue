<template>
  <main id="app" class="content">
    <nav class="findBar" ref="findBar" :style="showFindBar ? 'opacity:1' : 'opacity:0'">
      <input
        type        ="text"
        class       ="search"
        v-model     ="searchString"
        @keyup.enter="getWikiResults"
        placeholder ="Type and hit enter :)" />
      <footer class="footer">
        coded with <span class="love">&#9829; </span> by 
        <a href="https://twitter.com/SpaniardDev/" alt="Yago Estévez on Twitter">Yago Estévez</a>
      </footer>
    </nav>

    <transition name="fadeDisplay">
      <section class="initDisplay" v-if="searching">
        <h1 class="title" v-show="!searchString">Find in the Wiki</h1>
        <h1 class="title" v-show="searchString">Find: {{ searchString }}</h1>
        <p class="randomArticle">or just
          <a alt="Random Article" target="_blank"
              href="https://en.wikipedia.org/wiki/Special:Random"
          >click here</a> to read a random article
        </p>
      </section>
    </transition>

    <transition name="fadeResults">
      <section class="results" v-if="!searching">
        <results
          v-for   ="wiki in orderEntries"
          :key    ="wiki.index"
          :title  ="wiki.title"
          :extract="wiki.extract"
          :image  ="wiki.thumbnail?wiki.thumbnail.source:''"
          :link   ="getWikiURL( wiki.title )"
        ></results>
      </section>
    </transition>
  </main>
</template>

<script>
import _       from 'lodash'
import axios   from 'axios'
import Results from './components/Results.vue'

export default {
  name: 'App',
  components: { Results },
  data() {
    return {
      searchString  : '',
      searching     : true,
      showFindBar   : true,
      wikiResults   : [],
    }
  },
  methods: {
    showFindBaronBottom ( ) {
      if ( (window.innerHeight + window.scrollY ) <= document.body.offsetHeight-20 ) {
        this.showFindBar = true;
      } else {
        this.showFindBar = false;
      }
    },
    getWikiURL ( id ) {
      return "https://en.wikipedia.org/wiki/"+ encodeURI( id );
    },
    getWikiResults ( ) {
      this.wikiResults = [];
      const URL  = `https://en.wikipedia.org/w/api.php?action=query&format=json&prop=pageimages|extracts&generator=search&piprop=name|thumbnail&pithumbsize=250&exsentences=1&exlimit=max&exintro=1&explaintext=1&gsrnamespace=0&rawcontinue&gsrlimit=20&origin=*&gsrsearch=${ this.searchString }`;

      axios.get( URL )
        .then( results => {
          results = results.data.query.pages;
          for ( let entries in results ) {
            if ( results.hasOwnProperty( entries ) ) {
              let entry = results[ entries ];
              this.wikiResults.push( entry );
            }
          }
          this.searching = false
        } )
        .catch( err => console.log( err ) );
    }
  },
  computed: {
    orderEntries () {
      return _.orderBy( this.wikiResults, 'index' );
    }
  },
  created () {
    window.addEventListener( 'scroll', this.showFindBaronBottom );
  },
  destroyed () {
    window.removeEventListener( 'scroll', this.showFindBaronBottom );
  }
}
</script>

<style lang="scss">
  @import url('https://fonts.googleapis.com/css?family=Merriweather');

  // VARIABLES
  $white        : #eee;
  $gray         : #ddd;
  $darkGray     : #333;
  $black        : #111;
  $trBlack      : rgba(0, 0, 0, 0.40);
  $red          : crimson;
  $font-family  : Merriweather, serif;

  *,
  *::before,
  *::after {
    margin            : 0;
    padding           : 0;
    box-sizing        : border-box;
  }

  ::selection {
    background  : $black;
    color       : $gray;
  }

  html {
    font-size         : 100%;
  }

  body {
    background-color  : $gray;
    background-image  : url(./assets/bg.jpg);
    color             : $black;
    font-family       : $font-family;
    letter-spacing    : 0.04rem;
    display           : flex;
    flex-direction    : column;
    line-height       : 1.5rem;

    a:link,
    a:visited {
      text-decoration : none;
      color           : $black;
      text-decoration : underline;
      transition      : color 0.5s ease;
    }
    
    a:hover {
      color           : $red;
      text-decoration : none;
    }
  }

    .content {
      /*
      *  FINDBAR
      */
      .findBar {
        z-index     : 1000;
        position    : fixed;
        bottom      : 0;
        width       : 100%;
        text-align  : center;
        transition  : opacity 0.5s;

        .search {
          padding       : 1rem;
          font-family   : $font-family;
          font-size     : 1.2rem;
          color         : $black;
          width         : 90%;
          border-radius : 10px;
          border        : 1px solid $white;
          box-shadow    : 1px 1px 25px 3px $trBlack;
          text-align    : center;
          margin        : 1rem;
        }

        .footer {
          padding           : 0.3rem;
          background-color  : $black;
          color             : $white;
          font-size         : 0.6rem;
          letter-spacing    : 0.05rem;
          word-spacing      : 0.1rem;

          a:link,
          a:visited {
            text-decoration : none;
            color           : $white;
            transition      : color 0.5s ease;
          }

          a:hover {
            color           : $red;
          }
      
          .love {
            color           : $red;
            font-size       : 1.2rem;
            vertical-align  : middle;
            
          }

        }

      }

      /*
      *  DISPLAY
      */
      .initDisplay {
        position    : absolute;
        bottom      : 50%;
        width       : 100%;
        text-align  : center;

        .title {
          margin      : 0.5rem;
          font-size   : 2.5rem;
          line-height : 2.2rem;
        }

        .randomArticle {
          color       : $darkGray;
          font-size   : 0.8rem;
        }
      }

      /*
      *  RESULTS
      */
      .results {
        z-index     : 0;
        display     : flex;
        flex-flow   : row wrap;
        padding     : 2rem;
    
        .result {
          background-color  : $white;
          padding           : 1.5rem;
          margin            : 1rem;
          flex              : 1 1 20rem;
          text-align        : center;
          border-left       : 2px solid $darkGray;
          transition        : all 0.2s ease-out;

          &:hover {
            border-left     : 2px solid $red;
          }

          .image {
            clip-path       : circle(50% at 50% 50%);
            object-fit      : cover;
            width           : 250px;
            height          : 250px;
            transition      : all 0.2s ease-out;

            &:hover {
              transform     : scale(1.05) rotate(3deg);
            }
          }

          .title {
            margin      : 0.5rem;
          }

          .extract {
            text-align  : justify;
            hyphens     : auto;
            margin      : 0.5rem;
          }

          .wikiLink {
            margin      : 2rem 0;
          }

        }
      }
      
    }

    @media (max-width: 430px) {
      .image {
        width           : 100px !important;
        height          : 100px !important;
        clip-path       : circle(50% at 50% 50%);
      }
      .result {
        padding           : 0rem;
        margin            : 0rem;
      }
    }

    /*
    *  ANIMATIONS
    */
    .fadeResults-enter {
      opacity: 0;
    }
    .fadeResults-enter-active, .fadeResults-leave-active {
      transition: opacity 2s;
    }
    .fadeResults-leave-to {
      opacity: 0;
    }

    .fadeDisplay-enter {
      opacity: 0;
    }
    .fadeDisplay-enter-active, .fadeDisplay-leave-active {
      transition: opacity 1s;
    }
    .fadeDisplay-leave-to {
      opacity: 0;
    }
</style>
