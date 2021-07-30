<template>
  <div>
   <Header />
    <div class="bg-gray-400 h-screen pt-20 flex">
        <div class="w-1/5 bg-teal h-full p-3 fixed left-0 z-50">
            <input class="w-full bg-gray-400 text-white mb-2" type="text" v-model="search"/>
            <ul class="flex flex-col text-white">
                <li @click="selectGame(game, $event)" v-for="game in games" :key="game.id" class="collection-item cursor-pointer flex text-sm items-center hover:bg-blue-400 transition mb-1">
                    <img :src="`https://picsum.photos/2${game.id}`" alt="" class="mr-3 pointer-events-none">
                    <div class="pointer-events-none">{{ game.title }}</div> 
                </li>
            </ul>
        </div>
        <div class="w-4/5 bg-white h-full fixed right-0 overflow-y-scroll">
            <div class="relative">
                <div alt="" class="hero-pic w-full bg-cover" :style="`background-image:url('${heroGame.pictures[0]}');background-position: 0% 15%`"></div>
                <div class="banner absolute bottom-0 left-0 flex items-center px-4 py-2 w-full">
                    <a href="play" target="_blank"><button class="bg-green px-4 py-2 text-white uppercase font-bold flex items-center rounded mr-6">Jouer</button></a>
                    <div class="last-use mr-10 text-sm">
                        <div class="uppercase text-gray-100">Dernière utilisation</div>
                        <div class="text-gray-300">{{ heroGame.lastUse }}</div>
                    </div>
                    <div class="total-hours flex items-center mr-10 text-sm">
                        <img src="/welcome-on-board/dist/icons/time.png" alt="" class="mr-4 w-8 h-8">
                        <div>
                            <div class="uppercase text-gray-100">Temps de jeu</div>
                            <div class="text-gray-300">{{ heroGame.totalHours }}</div>
                        </div>
                    </div>
                    <div class="completion flex text-sm">
                        <img src="/welcome-on-board/dist/icons/cup.png" alt="" class="mr-4 w-10 h-10">
                        <div>
                            <div class="uppercase text-gray-100">Succès</div>
                            <div class="text-gray-300">{{ heroGame.completed }} / {{heroGame.achievements}}</div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="bg-teal pb-20 pt-8">
                <div class="tabs flex uppercase w-full justify-center mb-4 cursor-pointer">
                    <div @click="changeTab('activity', $event)" class="tabs-item cursor-pointer active activity w-1/5 text-center px-3 py-3 bg-gray mr-1 text-gray-300 text-sm">Activité</div>
                    <div @click="changeTab('achievements', $event)" class="tabs-item cursor-pointer achievements w-1/5 text-center px-3 py-3 bg-gray mr-1 text-gray-300 text-sm">Succès</div>
                    <div @click="changeTab('defis', $event)" class="tabs-item cursor-pointer defis px-3 py-3  w-1/5 text-center bg-gray text-gray-300 text-sm">Défis</div>
                </div>
                <div v-if="activeTab == 'activity'" class="px-3 py-2">
                    <h2 class="uppercase text-sm text-gray-300 mb-4">Activité</h2>
                    <div v-for="item in heroGame.activities" :key="item" class="mb-12">
                        <div class="flex items-center mb-3">
                            <div class="mr-3 w-10 h-10 bg-cover bg-center" :style="`background-image:url('${item.user.picture}');`"></div>
                            <div class="text-gray-300">
                                <span class="font-bold text-white">{{ item.user.name }}</span>
                                <span class="text-sm" v-if="item.isAchievementRelated"> a remporté un ou plusieurs succès</span>
                                <span class="text-sm" v-else> a remporté un ou plusieurs défis</span></div>
                        </div>
                        <div class="bg-gray flex flex-wrap p-3 ml-4">
                            <div class="w-1/3 flex items-center" v-for="item in item.items" :key="item">
                                <img :src="item.picture" alt="" class="mr-3"/>
                                <div>
                                    <div class="text-sm text-white">{{ item.name }}</div>
                                    <div class="text-xs text-gray-200">{{ item.description }}</div>
                                </div>
                            </div>    
                        </div>
                    </div>
                </div>
                <div v-if="activeTab == 'achievements'" class="px-3 py-2 flex flex-col pb-10">
                    <div class="flex mb-4 justify-between items-center">
                        <h2 class="uppercase text-sm text-gray-300">Succès</h2>
                        <div class="flex items-center">
                            <label class="text-sm text-white mr-4" for="progression">Taux de completion : <span>{{ getAchievementsCompletion() }} %</span></label>
                            <progress class="w3-blue" id="progression" max="100" :value="getAchievementsCompletion()"></progress>
                        </div>
                    </div>
                    <div class="bg-gray flex flex-wrap p-3">
                        <div class="w-1/3 flex items-center mb-3" v-for="item in heroGame.achievementsList" :key="item">
                            <img :src="item.picture" alt="" class="mr-3 bg-cover h-14 w-14" :class="{ 'opacity-10' : !item.unlocked}"/>
                            <div>
                                <div class="text-sm text-white">{{ item.name }}</div>
                                <div class="text-xs text-gray-200">{{ item.description }}</div>
                            </div>
                        </div>
                    </div>
                </div>
                <div v-if="activeTab == 'defis'" class="px-3 py-2 flex flex-col pb-10">
                    <div class="flex mb-4 justify-between items-center">
                        <h2 class="uppercase text-sm text-gray-300">Défis</h2>
                        <div class="flex items-center">
                            <label class="text-sm text-white mr-4" for="progression">Taux de completion : <span>{{ getDefisCompletion() }} %</span></label>
                            <progress class="w3-blue" id="progression" max="100" :value="getDefisCompletion()"></progress>
                        </div>
                    </div>
                    <div class="bg-gray flex flex-wrap p-3">
                        <div class="w-full flex justify-between items-center mb-4 pb-2 border-b border-gray-500" v-for="item in heroGame.defisList" :key="item">
                            <div class="flex items-center">
                                <img :src="item.picture" alt="" class="mr-3 w-14 h-14 bg-cover" :class="{ 'opacity-10' : !item.unlocked}"/>
                                <div>
                                    <div class="text-sm text-white">{{ item.name }}</div>
                                    <div class="text-xs text-gray-200">{{ item.description }}</div>
                                </div>
                            </div>
                            <div>
                                <button v-if="!item.unlocked" class="bg-blue px-4 py-2 text-white uppercase flex items-center rounded">Essayer</button>
                                <div v-else class="px-4 py-2 font-bold uppercase flex items-center rounded text-green-400">Complété</div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
        activeTab: 'activity',
        search: '',
        heroGame:  {
              id: 3,
              title: 'Love Letter',
              description: 'Qui saura utiliser au mieux la Cour pour délivrer un message à la Princesse ?',
              picture: '/welcome-on-board/dist/games/love-letter.jpeg',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false,
              pictures: ['/welcome-on-board/dist/games/love-letter-2.jpeg','/welcome-on-board/dist/games/love-letter.jpeg'],
                nbPlayers: '4-9',
                editor: 'Cocktail Games',
                authors: 'Aurelien Picolet',
                averageTime: '30min',  
                lastUse: '12 Octobre',
                totalHours: '5 heures',
                completed: '5',
                achievements: '7',

                 activities: [
                    {
                        date: 'Hier',
                        user: {
                            picture: '/faces/joseph.jpg',
                            name: 'Meeple'
                        },
                        isAchievementRelated: true,
                        items: [
                            {
                                picture: 'https://picsum.photos/53',
                                name: 'Les dés sont jetés',
                                description: 'Lancer 100 dés dans vos parties'
                            },
                            {
                                picture: 'https://picsum.photos/54',
                                name: 'C-C-C-Combo Breaker !',
                                description: 'Cocher 3 cases en une seule fois'
                            },
                            {
                                picture: 'https://picsum.photos/55',
                                name: 'Ramener la coupe à la maison',
                                description: "Être le vainqueur d'une partie"
                            }
                        ]
                    },
                    {
                        date: '9 Juillet',
                        user: {
                            picture: '/faces/hugues.png',
                            name: 'Hugues'
                        },
                        isAchievementRelated: true,
                        items: [
                            {
                                picture: 'https://picsum.photos/51',
                                name: 'Non, ça ne me plait pas',
                                description: 'Relancer vos dés 3 fois'
                            },
                            {
                                picture: 'https://picsum.photos/52',
                                name: 'Malin',
                                description: 'Obtenir 200 points en une partie'
                            }
                        ]
                    }
                ],
                achievementsList: [
                    {
                        name: 'Titre basique',
                        description: 'Vous avez lu ce titre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre basique mais pas tant',
                        description: 'Vous avez lu ce titre plus long que les autres',
                        picture: 'https://picsum.photos/31',
                        unlocked: false,
                    },
                    {
                        name: 'Titre lorem pisum ',
                        description: 'Vous avez lu ce titre aussi',
                        picture: 'https://picsum.photos/32',
                        unlocked: false,
                    },
                    {
                        name: 'Titre basico basique',
                        description: 'Vous avez lu ce titre',
                        picture: 'https://picsum.photos/33',
                        unlocked: true,
                    },
                    {
                        name: 'Titre acido-basique',
                        description: 'Vous avez lu ce diable de titre',
                        picture: 'https://picsum.photos/34',
                        unlocked: true,
                    },
                    {
                        name: 'Titre sous titre basique',
                        description: 'Vous avez lu ce titre a moitié avouez',
                        picture: 'https://picsum.photos/35',
                        unlocked: true,
                    },
                    {
                        name: 'Titre basique nique douille',
                        description: 'Vous avez lu ce titre pitre',
                        picture: 'https://picsum.photos/36',
                        unlocked: true,
                    }
                ],
                defisList: [
                    {
                        name: '4 cases en une fois',
                        description: 'Réussissez à cocher 4 cases à partir de la configuration donnée',
                        picture: 'https://picsum.photos/37',
                        unlocked: true,
                    },
                    {
                        name: 'Mettez vous au vert !',
                        description: 'Cochez 3 cases vertes en un tour pour ce défi',
                        picture: 'https://picsum.photos/38',
                        unlocked: false,
                    },
                    {
                        name: 'This is Sparta !',
                        description: 'Obtenez plus de 300 points à la fin du tour',
                        picture: 'https://picsum.photos/39',
                        unlocked: false,
                    },
                    {
                        name: 'La roue tourne va tourner',
                        description: 'Obtenez 250 points sans utiliser de relances',
                        picture: 'https://picsum.photos/40',
                        unlocked: true,
                    }
                ],
  
            },
        games: [
            {
              id: 1,
              title: 'Trés Futé',
              description: 'Un habile jeu de Roll&Write dans lequel seul le plus malin saura tirer profit de ses dés',
              picture: '/welcome-on-board/dist/games/tres-fute.jpeg',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false,
              pictures: ['/welcome-on-board/dist/games/tres-fute.jpeg','/welcome-on-board/dist/games/tres-fute-2.jpeg'],
                nbPlayers: '4-9',
                editor: 'Cocktail Games',
                authors: 'Aurelien Picolet',
                averageTime: '30min',
                lastUse: '14 Juillet',
                totalHours: '26,9 heures',
                completed: '5',
                achievements: '22',
                activities: [
                    {
                        date: 'Hier',
                        user: {
                            picture: '/faces/joseph.jpg',
                            name: 'Meeple'
                        },
                        isAchievementRelated: true,
                        items: [
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Les dés sont jetés',
                                description: 'Lancer 100 dés dans vos parties'
                            },
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'C-C-C-Combo Breaker !',
                                description: 'Cocher 3 cases en une seule fois'
                            },
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Ramener la coupe à la maison',
                                description: "Être le vainqueur d'une partie"
                            }
                        ]
                    },
                    {
                        date: '9 Juillet',
                        user: {
                            picture: '/faces/hugues.png',
                            name: 'Hugues'
                        },
                        isAchievementRelated: true,
                        items: [
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Non, ça ne me plait pas',
                                description: 'Relancer vos dés 3 fois'
                            },
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Malin',
                                description: 'Obtenir 200 points en une partie'
                            }
                        ]
                    }
                ],
                achievementsList: [
                    {
                        name: 'Titre basique',
                        description: 'Vous avez lu ce titre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre basique mais pas tant',
                        description: 'Vous avez lu ce titre plus long que les autres',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'Titre lorem pisum ',
                        description: 'Vous avez lu ce titre aussi',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'Titre basico basique',
                        description: 'Vous avez lu ce titre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre acido-basique',
                        description: 'Vous avez lu ce diable de titre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre sous titre basique',
                        description: 'Vous avez lu ce titre a moitié avouez',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre basique nique douille',
                        description: 'Vous avez lu ce titre pitre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    }
                ],
                defisList: [
                    {
                        name: '4 cases en une fois',
                        description: 'Réussissez à cocher 4 cases à partir de la configuration donnée',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Mettez vous au vert !',
                        description: 'Cochez 3 cases vertes en un tour pour ce défi',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'This is Sparta !',
                        description: 'Obtenez plus de 300 points à la fin du tour',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'La roue tourne va tourner',
                        description: 'Obtenez 250 points sans utiliser de relances',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    }
                ],

            },
            {
              id: 2,
              title: 'Kami',
              description: 'Un affrontement tactique entre deux à quatre joueurs dans un univers japonisant',
              picture: '/welcome-on-board/dist/games/kami.png',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false,
              pictures: ['/welcome-on-board/dist/games/kami.png','/welcome-on-board/dist/games/kami-2.jpeg'],
                nbPlayers: '4-9',
                editor: 'Cocktail Games',
                authors: 'Aurelien Picolet',
                averageTime: '30min',  
                lastUse: '10 Juin 2020',
                totalHours: '8,4 heures',
                completed: '11',
                achievements: '14',
                 activities: [
                    {
                        date: 'Hier',
                        user: {
                            picture: '/faces/joseph.jpg',
                            name: 'Meeple'
                        },
                        isAchievementRelated: true,
                        items: [
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Les dés sont jetés',
                                description: 'Lancer 100 dés dans vos parties'
                            },
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'C-C-C-Combo Breaker !',
                                description: 'Cocher 3 cases en une seule fois'
                            },
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Ramener la coupe à la maison',
                                description: "Être le vainqueur d'une partie"
                            }
                        ]
                    },
                    {
                        date: '9 Juillet',
                        user: {
                            picture: '/faces/hugues.png',
                            name: 'Hugues'
                        },
                        isAchievementRelated: true,
                        items: [
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Non, ça ne me plait pas',
                                description: 'Relancer vos dés 3 fois'
                            },
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Malin',
                                description: 'Obtenir 200 points en une partie'
                            }
                        ]
                    }
                ],
                achievementsList: [
                    {
                        name: 'Titre basique',
                        description: 'Vous avez lu ce titre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre basique mais pas tant',
                        description: 'Vous avez lu ce titre plus long que les autres',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'Titre lorem pisum ',
                        description: 'Vous avez lu ce titre aussi',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'Titre basico basique',
                        description: 'Vous avez lu ce titre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre acido-basique',
                        description: 'Vous avez lu ce diable de titre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre sous titre basique',
                        description: 'Vous avez lu ce titre a moitié avouez',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre basique nique douille',
                        description: 'Vous avez lu ce titre pitre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    }
                ],
                defisList: [
                    {
                        name: '4 cases en une fois',
                        description: 'Réussissez à cocher 4 cases à partir de la configuration donnée',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Mettez vous au vert !',
                        description: 'Cochez 3 cases vertes en un tour pour ce défi',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'This is Sparta !',
                        description: 'Obtenez plus de 300 points à la fin du tour',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'La roue tourne va tourner',
                        description: 'Obtenez 250 points sans utiliser de relances',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    }
                ],
            },
            {
              id: 3,
              title: 'Love Letter',
              description: 'Qui saura utiliser au mieux la Cour pour délivrer un message à la Princesse ?',
              picture: '/welcome-on-board/dist/games/love-letter.jpeg',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false,
              pictures: ['/welcome-on-board/dist/games/love-letter-2.jpeg','/welcome-on-board/dist/games/love-letter.jpeg'],
                nbPlayers: '4-9',
                editor: 'Cocktail Games',
                authors: 'Aurelien Picolet',
                averageTime: '30min',  
                lastUse: '12 Octobre',
                totalHours: '5 heures',
                completed: '5',
                achievements: '7',
                 activities: [
                    {
                        date: 'Hier',
                        user: {
                            picture: '/faces/joseph.jpg',
                            name: 'Meeple'
                        },
                        isAchievementRelated: true,
                        items: [
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Les dés sont jetés',
                                description: 'Lancer 100 dés dans vos parties'
                            },
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'C-C-C-Combo Breaker !',
                                description: 'Cocher 3 cases en une seule fois'
                            },
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Ramener la coupe à la maison',
                                description: "Être le vainqueur d'une partie"
                            }
                        ]
                    },
                    {
                        date: '9 Juillet',
                        user: {
                            picture: '/faces/hugues.png',
                            name: 'Hugues'
                        },
                        isAchievementRelated: true,
                        items: [
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Non, ça ne me plait pas',
                                description: 'Relancer vos dés 3 fois'
                            },
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Malin',
                                description: 'Obtenir 200 points en une partie'
                            }
                        ]
                    }
                ],
                achievementsList: [
                    {
                        name: 'Titre basique',
                        description: 'Vous avez lu ce titre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre basique mais pas tant',
                        description: 'Vous avez lu ce titre plus long que les autres',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'Titre lorem pisum ',
                        description: 'Vous avez lu ce titre aussi',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'Titre basico basique',
                        description: 'Vous avez lu ce titre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre acido-basique',
                        description: 'Vous avez lu ce diable de titre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre sous titre basique',
                        description: 'Vous avez lu ce titre a moitié avouez',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre basique nique douille',
                        description: 'Vous avez lu ce titre pitre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    }
                ],
                defisList: [
                    {
                        name: '4 cases en une fois',
                        description: 'Réussissez à cocher 4 cases à partir de la configuration donnée',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Mettez vous au vert !',
                        description: 'Cochez 3 cases vertes en un tour pour ce défi',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'This is Sparta !',
                        description: 'Obtenez plus de 300 points à la fin du tour',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'La roue tourne va tourner',
                        description: 'Obtenez 250 points sans utiliser de relances',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    }
                ],
  
            },
            {
              id: 4,
              title: 'Splendor',
              description: 'Une course aux points de victoire afin de devenir le meilleur joaillier du royaume',
              picture: '/welcome-on-board/dist/games/splendor.jpeg',
              price: '9,99€',
              tags: ['Cartes', 'Humour', 'Ambiance'],
              isOnDiscount: false,
              pictures: ['/welcome-on-board/dist/games/splendor.jpeg','/welcome-on-board/dist/games/splendor-2.jpeg'],
                nbPlayers: '4-9',
                editor: 'Cocktail Games',
                authors: 'Aurelien Picolet',
                averageTime: '30min',  
                lastUse: '20 Mars 2018',
                totalHours: '33,5 heures',
                completed: '17',
                achievements: '21',
                 activities: [
                    {
                        date: 'Hier',
                        user: {
                            picture: '/faces/joseph.jpg',
                            name: 'Meeple'
                        },
                        isAchievementRelated: true,
                        items: [
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Les dés sont jetés',
                                description: 'Lancer 100 dés dans vos parties'
                            },
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'C-C-C-Combo Breaker !',
                                description: 'Cocher 3 cases en une seule fois'
                            },
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Ramener la coupe à la maison',
                                description: "Être le vainqueur d'une partie"
                            }
                        ]
                    },
                    {
                        date: '9 Juillet',
                        user: {
                            picture: '/faces/hugues.png',
                            name: 'Hugues'
                        },
                        isAchievementRelated: true,
                        items: [
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Non, ça ne me plait pas',
                                description: 'Relancer vos dés 3 fois'
                            },
                            {
                                picture: 'https://picsum.photos/50',
                                name: 'Malin',
                                description: 'Obtenir 200 points en une partie'
                            }
                        ]
                    }
                ],
                achievementsList: [
                    {
                        name: 'Titre basique',
                        description: 'Vous avez lu ce titre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre basique mais pas tant',
                        description: 'Vous avez lu ce titre plus long que les autres',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'Titre lorem pisum ',
                        description: 'Vous avez lu ce titre aussi',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'Titre basico basique',
                        description: 'Vous avez lu ce titre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre acido-basique',
                        description: 'Vous avez lu ce diable de titre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre sous titre basique',
                        description: 'Vous avez lu ce titre a moitié avouez',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Titre basique nique douille',
                        description: 'Vous avez lu ce titre pitre',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    }
                ],
                defisList: [
                    {
                        name: '4 cases en une fois',
                        description: 'Réussissez à cocher 4 cases à partir de la configuration donnée',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    },
                    {
                        name: 'Mettez vous au vert !',
                        description: 'Cochez 3 cases vertes en un tour pour ce défi',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'This is Sparta !',
                        description: 'Obtenez plus de 300 points à la fin du tour',
                        picture: 'https://picsum.photos/30',
                        unlocked: false,
                    },
                    {
                        name: 'La roue tourne va tourner',
                        description: 'Obtenez 250 points sans utiliser de relances',
                        picture: 'https://picsum.photos/30',
                        unlocked: true,
                    }
                ],
  
            }
        ]
    }
  },
  methods: {
      selectGame(game, event) {
            let items = [...document.getElementsByClassName('collection-item')]
            for(let tab of items) {
              tab.classList.remove('active')
            }
            console.log(event.target);
            event.target.classList.add('active');
            console.log(event.target.classList);
            this.heroGame = game
      },
      changeTab(tab, event) {
          let tabs = [...document.getElementsByClassName('tabs-item')]
          let newActive;
          for(let tab of tabs) {
              tab.classList.remove('active')
          }
          event.target.classList.add('active')
          this.activeTab = tab
      },
      getAchievementsCompletion() {
          let completed = this.heroGame.achievementsList.filter((item) => item.unlocked == true)
          let total = this.heroGame.achievementsList.length
          let result = Math.floor(completed.length/total*100) 
          return result;
      },
      getDefisCompletion() {
          let completed = this.heroGame.defisList.filter((item) => item.unlocked == true)
          let total = this.heroGame.defisList.length
          let result = Math.floor(completed.length/total*100) 
          return result;
      }
  }
}
</script>

<style>
    .banner {
        background: linear-gradient(to bottom, #5b5c5e, #3b3c3d);
        opacity: 0.9;
    }
    .bg-gray {
        background: linear-gradient(to bottom, #5b5c5e, #3b3c3d);
    }
    .banner button {
        &::before {
            content: '';
            display: inline-block;
            width: 0;
            height: 0;
            border-style: solid;
            border-width: 7.5px 0 7.5px 15px;
            border-color: transparent transparent transparent white;
            margin-right: 10px;
        }
    }
    .tabs .active {
        @apply font-bold;
    }
    progress::-webkit-progress-value { 
        background: rgba(96, 165, 250);
        border-radius: 2px;
        height: 10px;
    }
    progress::-webkit-progress-bar { 
        border-radius: 2px;
        height: 10px;
        margin-top: 4px;
    }
    .collection-item.active{
        @apply bg-blue-400;
    }
    .bg-blue {
        background: linear-gradient(to bottom, #3066d3, #157be0);
    }
    .hero-pic {
        height: 20rem;
    }
    .bg-green {
        background: linear-gradient(to right, #73D01E, #69bd1b);
    }
    .bg-teal {
        background: linear-gradient(to right, #2a475e, #254e6d, #2a475e);
    }
    .bg-tealer {
        background-color: #173247;
    }

</style>
