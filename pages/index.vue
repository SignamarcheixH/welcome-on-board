<template>
  <div>
   <Header />
   <div class="bg-teal pt-20 text-cream">
     <div class="px-10">
       <div class="py-6 decorated-text text-xl"><span>A la une</span></div>
      <div class="hero-game flex justify-center mb-10">
        <div class="pictures" style="height: 700px;">
          <img :src="heroGame.pictures[0]" alt="" class="h-full">
        </div>
        <div class="infos bg-tealer p-4 w-1/4">
          <h1 class="uppercase text-2xl font-bold">{{ heroGame.title }}</h1>
          <div class="text-sm italic pt-3">{{ heroGame.description }}</div>
          <ul class="tags flex overflow-scroll mt-4 mb-8">
            <li class="rounded-full bg-gray-700 text-xs py-2 px-4 mr-3" v-for="tag in heroGame.tags" :key="tag">{{ tag }}</li>
          </ul>
          <div class="flex items-center mb-6">
            <img class="mr-3 w-8 h-8" src="/icons/players.png" alt="">
            <div>{{ heroGame.nbPlayers }}</div>
          </div>
          <div class="flex items-center mb-6">
            <img class="mr-3 w-8 h-8" src="/icons/time.png" alt="">
            <div>{{ heroGame.averageTime }}</div>
          </div>
          <div class="flex items-center mb-6">
            <img class="mr-3 w-8 h-8" src="/icons/brain.png" alt="">
            <div>{{ heroGame.authors }}</div>
          </div>
          <div class="flex items-center mb-6">
            <img class="mr-3 w-8 h-8" src="/icons/house.png" alt="">
            <div>{{ heroGame.editor }}</div>
          </div>
          <div>{{ heroGame.price }}</div>
        </div>
      </div>
      <div v-for="row in rows" :key="row.title" class="py-10">
        <div>
          <h2 class="decorated-text text-xl">{{ row.title }}</h2>
          <p class="italic mb-6">{{ row.description }}</p>
          <ul class="flex items-center">
            <li v-for="game in row.games" :key="game.id" class="game-card w-1/4 mr-12 cursor-pointer">
              <div class="relative">
                <div :src="game.picture" alt="" class="w-full bg-cover bg-top" :style="`background-image:url('${game.picture}');height: 300px`"></div>
                <div class="infos bg-gray-900 h-full absolute top-0 left-0 p-3">
                  <div class="name text-lg font-bold uppercase">{{ game.title }}</div>
                  <div class="desc my-4">{{ game.description }}</div>
                  <ul class="flex overflow-scroll">
                    <li class="rounded-full bg-gray-700 text-xs py-1 px-2 mr-2" v-for="tag in game.tags" :key="tag">{{ tag }}</li>
                  </ul>
                </div>
                <div class="price absolute bottom-0 right-0 bg-green mb-6 pr-10 rounded-l px-4 py-1 opacity-0"><div class="w-full text-center">{{ game.price }}</div></div>
              </div>
            </li>
          </ul>
        </div>
      </div>
     </div>
   </div>
   <Footer />
  </div>
</template>

<script>
export default {
  data() {
    return {
      heroGame: {
        title: 'Top Ten',
        description: "Un jeu d'ambiance dans lequel vous pourrez laisser livre cours à votre imagination",
        price: '9,99€',
        isOnDiscount: false,
        pictures: ['/games/topten.jpeg','https://picsum.photos/201'],
        tags: ['Cartes', 'Humour', 'Ambiance', 'Imagination'],
        nbPlayers: '4-9',
        editor: 'Cocktail Games',
        authors: 'Aurelien Picolet',
        averageTime: '30min'
      },
      rows: [
        {
          title: 'Les jeux du moment',
          description : 'Les jeux les plus joués en ce moment par notre communauté',
          games: [
            {
              id: 1,
              title: 'Trés Futé',
              description: 'Un habile jeu de Roll&Write dans lequel seul le plus malin saura tirer profit de ses dés',
              picture: '/games/tres-fute.jpeg',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false
            },
            {
              id: 2,
              title: 'Kami',
              description: 'Un affrontement tactique entre deux à quatre joueurs dans un univers japonisant',
              picture: '/games/kami.png',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false
            },
            {
              id: 3,
              title: 'Love Letter',
              description: 'Qui saura utiliser au mieux la Cour pour délivrer un message à la Princesse ?',
              picture: '/games/love-letter-2.jpeg',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false
            },
            {
              id: 4,
              title: 'Splendor',
              description: 'Une course aux points de victoire afin de devenir le meilleur joaillier du royaume',
              picture: '/games/splendor.jpeg',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false
            }
          ]
        },
        {
          title: "Les jeux d'ambiance",
          description : "Des jeux misant plus sur l'ambiance que sur la stratégie pure",
          games: [
            {
              id: 4,
              title: 'Splendor',
              description: 'Une course aux points de victoire afin de devenir le meilleur joaillier du royaume',
              picture: '/games/splendor.jpeg',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false
            },
              {
                id: 3,
                title: 'Love Letter',
                description: 'Qui saura utiliser au mieux la Cour pour délivrer un message à la Princesse ?',
                picture: '/games/love-letter-2.jpeg',
                price: '9,99€',
                tags: ['Cartes', 'Humour', 'Ambiance'],
                isOnDiscount: false
              },
            {
              id: 2,
              title: 'Kami',
              description: 'Un affrontement tactique entre deux à quatre joueurs dans un univers japonisant',
              picture: '/games/kami.png',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false
            },
              {
                id: 1,
                title: 'Trés Futé',
                description: 'Un habile jeu de Roll&Write dans lequel seul le plus malin saura tirer profit de ses dés',
                picture: '/games/tres-fute.jpeg',
                price: '9,99€',
                tags: ['Cartes', 'Humour', 'Ambiance'],
                isOnDiscount: false
              },
          ]
        },
        {
          title: "Les jeux de stratégie",
          description : "Laissez parler votre cerveau pour surpasser vos adversaires dans cette selection de jeux stratégiques",
          games: [
            {
              id: 1,
              title: 'Trés Futé',
              description: 'Un habile jeu de Roll&Write dans lequel seul le plus malin saura tirer profit de ses dés',
              picture: '/games/tres-fute.jpeg',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false
            },
              {
                id: 3,
                title: 'Love Letter',
                description: 'Qui saura utiliser au mieux la Cour pour délivrer un message à la Princesse ?',
                picture: '/games/love-letter-2.jpeg',
                price: '9,99€',
                tags: ['Cartes', 'Humour', 'Ambiance'],
                isOnDiscount: false
              },
              {
                id: 4,
                title: 'Splendor',
                description: 'Une course aux points de victoire afin de devenir le meilleur joaillier du royaume',
                picture: '/games/splendor.jpeg',
                price: '9,99€',
                tags: ['Cartes', 'Humour', 'Ambiance'],
                isOnDiscount: false
              },
            {
              id: 2,
              title: 'Kami',
              description: 'Un affrontement tactique entre deux à quatre joueurs dans un univers japonisant',
              picture: '/games/kami.png',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false
            },
          ]
        },
        {
          title: "Les jeux de cartes",
          description : "Des jeux utilisant des cartes comme principal matériel",
          games: [
            {
              id: 2,
              title: 'Kami',
              description: 'Un affrontement tactique entre deux à quatre joueurs dans un univers japonisant',
              picture: '/games/kami.png',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false
            },
            {
              id: 1,
              title: 'Trés Futé',
              description: 'Un habile jeu de Roll&Write dans lequel seul le plus malin saura tirer profit de ses dés',
              picture: '/games/tres-fute.jpeg',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false
            },
            {
                id: 4,
                title: 'Splendor',
                description: 'Une course aux points de victoire afin de devenir le meilleur joaillier du royaume',
                picture: '/games/splendor.jpeg',
                price: '9,99€',
                tags: ['Cartes', 'Humour', 'Ambiance'],
                isOnDiscount: false
              },
            {
              id: 3,
              title: 'Love Letter',
              description: 'Qui saura utiliser au mieux la Cour pour délivrer un message à la Princesse ?',
              picture: '/games/love-letter-2.jpeg',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false
            },
          ]
        }
      ]
    }
  }
}
</script>

<style>
  .bg-teal {
    background: linear-gradient(to right, #2a475e, #254e6d, #2a475e);
  }
  .bg-tealer {
    background-color: #173247;
  }
  .text-cream {
    color: #FFFAFA
  }
  .decorated-text {
    @apply flex items-center justify-center;
    &::before {
     content:'';
     display: inline-block;
     width: 40%;
     height: 1px;
     background-color: white;
     margin-right: 3%;
    }
    &::after {
     content:'';
     display: inline-block;
     width: 40%;
     height: 1px;
     background-color: white; 
     margin-left: 3%;
    }
  }
  .game-card:hover .infos {
    opacity: 0.8;
  }
  .game-card:last-child {
    margin: 0;
  }
  .game-card:hover .price {
    opacity: 1;
    transition: 0.3s;
  }
  .game-card .infos {
    opacity: 0;
    transition: 0.3s;
  }
  .bg-green {
    background: linear-gradient(to right, #73D01E, #69bd1b);
  }
</style>
