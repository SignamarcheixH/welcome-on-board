<template>
  <div class="w-full max-h-screen min-h-screen relative">
        <div class="menu flex flex-col absolute top-0 left-1/2 transition z-50">
            <div class="actions flex px-4 py-4 rounded-bl-lg border-b-2 border-l-2 border-r-2" style="background-color: #E8EFF4">
                <div class="h-12 w-20 mr-10 logo"><img src="/logo.png" alt="" class="h-full bg-cover"/></div>
                <div @click="openInvite()" class="h-12 w-12 rounded-full p-2 mr-10 invite cursor-pointer"><img src="/welcome-on-board/dist/icons/send.png" alt="" class="h-full"/></div>
                <div class="h-12 w-12 rounded-full p-2 mr-10 rules cursor-pointer"><img src="/welcome-on-board/dist/icons/book.png" alt="" class="h-full"/></div>
                <div class="h-12 w-12 rounded-full p-2 leave cursor-pointer"><img src="/welcome-on-board/dist/icons/leave.png" alt="" class="h-full"/></div>
            </div>
            <div class="self-end flex" style="transform: translateY(-2px)">
                <div class="fake border-t-2 border-r-2 rounded-tr w-8 h-6 z-50"></div>
                <div @click="openMenu" class="arrow-down cursor-pointer border-r-2 rounded-b-lg border-b-2 transform border-l-2 px-4 pt-4 pb-3" style="background-color: #E8EFF4"></div>
            </div>
        </div>
        <div class="scores flex absolute top-1/3 left-0 transition transform -translate-y-8 z-50">
            <div class="data pr-4 pt-4 pb-6 rounded-tr-lg border-t-2 border-r-2 border-b-2 w-52 flex flex-col justify-center" style="background-color: #E8EFF4">
                <div class="font-bold text-gray-700 mb-4 text-center">Tour {{ nbTour }}</div>
                <ul class="players text-gray-700 border-t">
                    <li v-for="player in players" :key="player" class="border-b">
                        <div class="flex">
                            <div class="flex flex-col items-center border-r mr-3 w-1/2">
                                <img :src="player.picture" alt="" class="rounded-full w-8 h-8 bg-cover bg-center" />
                                <div class="text-xs">{{ player.name }}</div>
                            </div>
                            <div class="w-1/2 points flex items-center justify-between font-bold">
                                <div>{{ player.points }}</div>
                                <div v-if="activePlayer && activePlayer.name == player.name" class="rounded-full bg-green w-2 h-2"></div>
                                <div v-if="deadPlayers.includes(player.name)" class="rounded-full bg-red-500 w-2 h-2"></div>
                            </div>
                        </div>
                    </li>
                </ul>
                <button v-if="!gameLaunched" @click="launchGame()" class="bg-green px-4 py-2 text-white uppercase text-sm mt-6 rounded mx-auto">Commencer</button>
                <button v-else @click="endTour()" class="bg-green px-4 py-2 text-white uppercase text-sm mt-6 rounded mx-auto">Fin du tour</button>
            </div>    
            <div class="self-end flex flex-col" style="transform: translateX(-2px)">
                <div class="fake border-l-2 border-b-2 rounded-bl w-8 h-6 z-50" style="transform: translateY(2px)"></div>
                <div @click="openScores" class="arrow-right cursor-pointer border-r-2 rounded-r-lg border-b-2 border-t-2 transform  px-4 pt-4 pb-3" style="background-color: #E8EFF4"></div>
            </div> 
        </div> 
        <aside draggable="true" id="dragme" class="p-0 camera">
            <div class="relative pointer-events-none h-full">
                <div class="absolute bottom-0 w-full text-center bg-gray text-white">{{ players[0].name }}</div>
                <img :src="players[0].picture" alt="" class="bg-cover h-full w-full">
            </div>
        </aside>
        <div class="p-0 absolute camera right-0 top-0">
            <div class="relative pointer-events-none h-full">
                <div class="absolute bottom-0 w-full text-center bg-gray text-white">{{ players[1].name }}</div>
                <img :src="players[1].picture" alt="" class="bg-cover h-full w-full">
            </div>
        </div>
        <div v-if="firstInviteDone" class="p-0 absolute camera bottom-0 right-0">
            <div class="relative pointer-events-none h-full">
                <div class="absolute bottom-0 w-full text-center bg-gray text-white">{{ players[2].name }}</div>
                <img :src="players[2].picture" alt="" class="bg-cover h-full w-full">
            </div>
        </div>
        <div v-show="isModalInviteOpen" class="modal popup-invite h-screen w-screen absolute top-0 left-0 z-50">
            <div class="bg-gray-900 opacity-75 w-full h-full"></div>
            <div class="inside border-2 text-white absolute text-center top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 bg-teal rounded shadow px-6 py-4">
                <div class="relative">
                    <h2 class="text-h2 font-bold mb-4">Invitez vos amis !</h2>
                    <p class="text-sm italic mb-6">Copiez cette URL et envoyez-la à vos amis. Ils pourront vous rejoindre en suivant ce lien.</p>
                    <div class="text-xs text-left text-gray-300">Lien de la salle WelcomeOnBoard : </div>
                    <div class="bg-gray py-2 text-sm text-gray-300">https://welcomeonboard.fr/play/HGIS2346AHSU2</div>
                    <div @click="closeInvite()" class="button-close rounded-full h-10 w-10 bg-gray-300 text-gray-800 absolute bottom-full left-full flex items-center justify-center text-2xl">
                        <span class="cross text-gray-500" style="transform: translate(1px,-2px)">x</span>
                    </div>
                </div>
            </div>
        </div>
        <div v-show="isModalAcceptInvite" class="modal popup-accept h-screen w-screen absolute top-0 left-0 z-50">
            <div class="bg-gray-900 opacity-75 w-full h-full"></div>
            <div class="inside border-2 text-white absolute text-center top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 bg-teal rounded shadow px-6 py-4">
                <div class="relative">
                    <h2 class="text-h2 font-bold mb-4">Quelqu'un veut rejoindre ce salon</h2>
                    <p class="text-sm italic mb-6">L'utilisateur <span class="font-bold text-blue-400">Meeple</span> veut rejoindre ce salon. </p>
                    <div class="flex justify-between">
                        <button @click="acceptInvite()" class="w-1/2 bg-green px-4 py-2 text-white uppercase font-bold rounded mr-6">Accepter</button>
                        <div class="w-1/2 bg-gray px-4 py-2 text-sm text-gray-300 uppercase flex items-center justify-center">Refuser</div>
                    </div>
                    <div></div>
                    <div @click="closeInvite()" class="button-close rounded-full h-10 w-10 bg-gray-300 text-gray-800 absolute bottom-full left-full flex items-center justify-center text-2xl">
                        <span class="cross text-gray-500" style="transform: translate(1px,-2px)">x</span>
                    </div>
                </div>
            </div>
        </div>
        <div v-show="isModalChoosePlayer" class="modal player-selection h-screen w-screen absolute top-0 left-0 z-50">
            <div class="bg-gray-900 opacity-75 w-full h-full"></div>
            <div class="inside border-2 text-white absolute text-center top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 bg-teal rounded shadow px-6 py-4">
                <div class="relative">
                    <h2 class="text-h2 font-bold mb-4">Choisissez un joueur</h2>
                    <ul class="players choices text-white border-t">
                        <li v-for="player in players.filter((el) => el.name != 'Vous')" :key="player.name" :data-name="player.name" class="border-b hover:bg-blue-400 cursor-pointer">
                            <div class="flex pointer-events-none">
                                <div class="flex flex-col items-center border-r mr-3 w-1/2">
                                    <img :src="player.picture" alt="" class="rounded-full w-8 h-8 bg-cover bg-center" />
                                    <div class="text-xs">{{ player.name }}</div>
                                </div>
                                <div class="w-1/2 points flex items-center justify-between font-bold">
                                    <div>{{ player.points }}</div>
                                    <div v-if="activePlayer && activePlayer.name == player.name" class="rounded-full bg-green w-2 h-2"></div>
                                </div>
                            </div>
                        </li>
                    </ul>    
                </div>
            </div>
        </div>

        <div v-show="isModalComparingOpen" class="modal player-comparing h-screen w-screen absolute top-0 left-0 z-50">
            <div class="bg-gray-900 opacity-75 w-full h-full"></div>
            <div class="player-card absolute top-1/3 right-2/3 transition"></div>
            <div class="opponent-card absolute top-1/3 left-2/3 transition"></div>
        </div>

        <div v-if="isModalStartTourOpened" class="modal popup-starttour h-screen w-screen absolute top-0 left-0 z-50">
            <div class="inside border-2 text-white absolute text-center top-0 left-1/2 transform translate-y-1/2 bg-teal rounded shadow px-6 py-4 w-1/2">
                    <p class="text-sm italic">C'est a <span class="font-bold text-blue-400">{{ activePlayer.name }}</span> de jouer</p>
            </div>
        </div>
        <div v-if="isModalInfoTour" class="modal popup-infoTour h-screen w-screen absolute top-0 left-0 z-50">
            <div class="inside border-2 text-white absolute text-center top-0 left-1/2 transform translate-y-1/2 bg-teal rounded shadow px-6 py-4 w-1/2">
                    <p class="text-sm italic" v-html="modalInfoTourMessage"></p>
            </div>
        </div>

        <div class="board w-full min-h-screen">
            <div class="playingCards max-h-screen min-h-screen">
                <div class="hand-area player-hand absolute bottom-0 left-1/2 transform -translate-x-28 w-full">
                    <ul class="hand">
                        <li v-for="card in hand" :key="card">
                            <span @click="playCard(card, $event)" class="card rank-a" :data-value="card.value">
                                <img class="pointer-events-none" :src="card.picture" alt=""/>
                            </span>
                        </li>
                    </ul>
                </div>
                <div class="hand-area north absolute top-0 left-1/2 transform -translate-x-12 translate-y-6 w-full">
                    <ul class="opponent-hand">
                        <li v-for="card in opponentHands['Aleksandra']" :key="card">
                            <div class="card back">*</div>
                        </li>
                    </ul>
                </div>
                <div class="hand-area east absolute right-0 top-1/2 transform -translate-y-6 -translate-x-6">
                    <ul class="opponent-hand">
                        <li v-for="card in opponentHands['Guest #1234']" :key="card">
                            <div class="card back transform rotate-90">*</div>
                        </li>
                    </ul>
                </div>
                <div class="hand-area west absolute left-0 top-1/2 transform -translate-y-6 translate-x-6">
                    <ul class="opponent-hand">
                        <li v-for="card in opponentHands['Meeple']" :key="card">
                            <div class="card back transform rotate-90">*</div>
                        </li>
                    </ul>
                </div>
                <ul class="deck absolute top-1/2 right-1/3 transform -translate-y-1/2 z-50" >
                    <li v-for="i in 10" :key="i"><div class="card back transition">*</div></li>
                    <li class="moving-card transition duration-700 ease-in-out"><div class="card back">*</div></li>
                </ul>
                <ul class="trash absolute top-1/2 right-2/3 transform -translate-y-1/2 border-2 z-50">
                    
                </ul>
            </div>
        </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
        isModalInviteOpen: false,
        isModalStartTourOpened: false,
        isModalInfoTour: false,
        isModalChoosePlayer: false,
        isModalComparingOpen: false,
        modalInfoTourMessage: '',
        nbTour: 1,
        firstInviteDone: false,
        isModalAcceptInvite: false,
        gameLaunched: false,
        activePlayer: null,
        players: [
            {
                name: 'Aleksandra',
                picture: '/welcome-on-board/dist/faces/aleksandra.jpg',
                points: '0'
            },
            {
                name: 'Guest #1234',
                picture: '/welcome-on-board/dist/faces/alexandra.jpg',
                points: '0'
            },
            {
                name: 'Meeple',
                picture: '/welcome-on-board/dist/faces/joseph.jpg',
                points: '0'
            },
            {
                name: 'Vous',
                picture: '/welcome-on-board/dist/faces/hugues.png',
                points: '0'
            },
        ],
        deadPlayers: [],
        hand: [],
        opponentHands: {
          'Meeple': [],
          'Guest #1234': [],
          'Aleksandra': [] 
        },
        cardsToPlay: {
            self: [
                {
                    name: 'Baron',
                    value: '3',
                    picture : '/welcome-on-board/dist/cards/baron.png'
                },
                {
                    value: '5',
                    name: 'Prince',
                    picture: '/welcome-on-board/dist/cards/prince.png'
                },
                {   
                    name: 'Garde',
                    value: '1',
                    picture : '/welcome-on-board/dist/cards/garde.png'
                },
            ],
            players: [
                {
                    value: '4',
                    name: 'Servante',
                    picture: '/welcome-on-board/dist/cards/servante.png'
                },
                {
                    value: '1',
                    name: 'Garde',
                    picture: '/welcome-on-board/dist/cards/garde.png'
                },
                {
                    value: '1',
                    name:'Garde',
                    picture: '/welcome-on-board/dist/cards/garde.png'
                },
                {
                    value: '3',
                    name:'Baron',
                    picture: '/welcome-on-board/dist/cards/baron.png'
                }
            ]
        }
    }
  },
  mounted() {
      this.initDraggable();
  },
  methods: {
    playCard(card, event) {
        event.target.classList.add('selected')
        this.modalInfoTourMessage = `<span class="text-blue-400">${this.activePlayer.name}</span> joue la carte <span class="text-yellow-200">${card.name}</span>`
        this.openInfoTourModal()
        switch(card.value) {
            case '1':
                this.doGardeAction()
                break;
            case '3':
                this.doBaronAction()
                break;
            case '4':
                this.doServanteAction()
                break;
            case '5':
                this.doPrinceAction()
                break;
        }
    },
    doGardeAction() {
        this.throwSelectedCard()
    },
    doServanteAction() {
        this.throwSelectedCard()
    },
    doBaronAction() {
        this.choosePlayer().then((selected) => {
            this.processBaron(selected)
        })
    },
    processBaron(selected) {
        this.modalInfoTourMessage = `<span class="text-blue-400">${this.activePlayer.name}</span> et <span class="text-blue-400">${selected}</span> comparent leurs mains.`
        this.openInfoTourModal()
        this.throwSelectedCard()
        this.openComparingModal(selected) 
    },
    openComparingModal(opponentName) {
        this.isModalComparingOpen = true
        let playerCard = this.hand[0].picture
        let templatePlayer = `<span class="card rank-a">
                            <img class="pointer-events-none" src="${playerCard}" alt=""/>
                        </span>`
        document.getElementsByClassName('player-card')[0].innerHTML = templatePlayer

        let opponentCard = this.opponentHands[opponentName][0].picture
        let templateOpponent = `<span class="card rank-a">
                            <img class="pointer-events-none" src="${opponentCard}" alt=""/>
                        </span>`
        document.getElementsByClassName('opponent-card')[0].innerHTML = templateOpponent
        
        let playerValue = parseInt(this.hand[0].value)
        let opponentValue = parseInt(this.opponentHands[opponentName][0].value)
        let loser = ''
        let tie = false
        setTimeout(() => {
            if(playerValue > opponentValue) {
                loser = opponentName
                document.getElementsByClassName('player-card')[0].classList.add('winner')
                document.getElementsByClassName('opponent-card')[0].classList.add('loser')
                this.killPlayer(opponentName)
            } else if(opponentValue > playerValue) {
                loser = this.players[3].name
                document.getElementsByClassName('player-card')[0].classList.add('loser')
                document.getElementsByClassName('opponent-card')[0].classList.add('winner')
            } else {
                tie = true
            }
            setTimeout(()=> {
                this.isModalComparingOpen = false
                this.modalInfoTourMessage = (tie) ? "Egalité ! Personne n'a quitté la manche" : `<span class="text-blue-400">${loser}</span> quitte la manche. Il avait un(e) ${this.opponentHands[opponentName][0].name}`
                this.openInfoTourModal()
            }, 1500)
        }, 2000)
    },
    killPlayer(opponentName) {
        this.deadPlayers.push(opponentName)
        let classToTarget = ''
        switch(opponentName) {
            case 'Aleksandra':
                classToTarget = 'north'
                break;
            case 'Guest #1234':
                classToTarget = 'east'
                break;
            case 'Meeple':
                classToTarget = 'west'
                break;
        }

        let realCard = document.getElementsByClassName(classToTarget)[0].getElementsByClassName('card')[0]
        console.log('realCard : ')
        console.log(realCard)
        let cardToDiscard = document.getElementsByClassName('player-hand')[0].getElementsByClassName('card')[0]
        console.log('cardToDiscrad : ')
        console.log(cardToDiscard)
        let clone = cardToDiscard.cloneNode(true);
        clone.getElementsByTagName('img')[0].src = this.opponentHands[opponentName][0].picture
        
        let added = document.getElementsByClassName('trash')[0].appendChild(clone);
        
        realCard.classList.add('played')
            added.style.opacity = 0;
        setTimeout(() => {
            realCard.parentNode.remove()
        },300)
        setTimeout(() => {
           added.style.opacity = 1 
        }, 500)

        setTimeout(() => {
            this.nextTour()
        }, 5000)
    },
    nextTour() {
        this.activePlayer = this.players[2]
        this.openStartTourModal()
        this.dealCards()
    },
    choosePlayer() {
        this.openPlayerSelection()
        let clicked = '';
        let children = document.getElementsByClassName('player-selection')[0].getElementsByClassName('choices')[0].childNodes
        for(let item of children) {
            item.addEventListener('click', (event) => {
                this.closePlayerSelection()
                clicked = event.target.dataset.name
            })
        }
        var promise = new Promise(function(resolve, reject) {
            window.setTimeout(function() {
                resolve(clicked);
            },4000);
        });
        return promise;
    },
    doPrinceAction() {
        this.throwSelectedCard()
    },
    throwSelectedCard () {
        let selected = document.getElementsByClassName('player-hand')[0].getElementsByClassName('selected')[0] 
        let clone = selected.cloneNode(true);
        let added = document.getElementsByClassName('trash')[0].appendChild(clone);
    
        let idx = this.hand.findIndex(p => p.value == selected.dataset.value);
        let removed = this.hand.splice(idx,1);
        
        selected.classList.add('played')
            added.style.opacity = 0;
        setTimeout(() => {
            selected.parentNode.remove()
        },300)
        setTimeout(() => {
           added.style.opacity = 1 
        }, 500)
    },
    launchGame() {
        this.gameLaunched = true;
        this.launchTour(true)
    },
    launchTour(first = false) {
        this.dealInitialCards()
        setTimeout(() => {
            this.openStartTourModal()
            this.activePlayer = this.players[3]
            this.dealCards()
        }, 8000)
    },
    dealInitialCards() {
        this.activePlayer = this.players[3]
        this.dealCards()
        setTimeout(()=> {
            this.activePlayer = this.players[0]
            this.dealCards()
            setTimeout(()=> {
                this.activePlayer = this.players[1]
                this.dealCards()
                setTimeout(()=> {
                    this.activePlayer = this.players[2]
                    this.dealCards()
                },2000)
            },2000)
        },2000)
    },
    dealCards() {
        let card = document.getElementsByClassName('moving-card')[0]
        if(this.activePlayer.name == 'Vous') {
            card.classList.add('dealt-to-player')
            setTimeout(() => {
                card.style.opacity = 0
                setTimeout(() => {
                    card.classList.remove('dealt-to-player')
                    setTimeout(() => {
                        card.style.opacity = 1
                    }, 500)
                },400)
                this.addCartToHand()
            }, 600)
        } else {
            let classToAdd = '';
            switch(this.activePlayer.name) {
                case 'Aleksandra':
                    classToAdd = 'dealt-to-north'
                    break;
                case 'Guest #1234':
                    classToAdd = 'dealt-to-east'
                    break;
                case 'Meeple':
                    classToAdd = 'dealt-to-west'
                    break;
            }
            card.classList.add(classToAdd)
             setTimeout(() => {
                card.style.opacity = 0
                setTimeout(() => {
                    card.classList.remove('dealt-to-north', 'dealt-to-east', 'dealt-to-west')
                    setTimeout(() => {
                        card.style.opacity = 1
                    }, 500)
                },400)
                this.addCartToOpponentHand(this.activePlayer.name)
            }, 600)
        }
    },
    addCartToHand() {
        this.hand.push(this.cardsToPlay.self.shift());
    },
    addCartToOpponentHand(name) {
        this.opponentHands[name].push(this.cardsToPlay.players.shift());
    },
    openStartTourModal() {
        this.isModalStartTourOpened = true
        setTimeout(() => {
            document.getElementsByClassName('popup-starttour')[0].classList.add('open')
        }, 200)
        setTimeout(() => {
            document.getElementsByClassName('popup-starttour')[0].classList.remove('open')
        }, 1300)
    },
    openInfoTourModal() {
        console.log('modal info opened')
        this.isModalInfoTour = true
        setTimeout(() => {
            document.getElementsByClassName('popup-infoTour')[0].classList.add('open')
        }, 200)
        setTimeout(() => {
            document.getElementsByClassName('popup-infoTour')[0].classList.remove('open')
        }, 1300)
    },
    closeInvite() {
        this.isModalInviteOpen = false
        setTimeout(() => {
            if(!this.firstInviteDone) {
                this.firstInvite()
            }
        }, 1500)
    },
    firstInvite() {
        this.isModalAcceptInvite = true
    },
    acceptInvite() {
        this.isModalAcceptInvite = false
        this.firstInviteDone = true
    },
    openInvite() {
        this.isModalInviteOpen = true
    },  
    openMenu() {
        document.getElementsByClassName('menu')[0].classList.toggle('active')
    },
    openPlayerSelection() {
        this.isModalChoosePlayer = true
    },
    closePlayerSelection() {
        this.isModalChoosePlayer = false
    },
    openScores() {
        document.getElementsByClassName('scores')[0].classList.toggle('active')
    },
    initDraggable() {
        var dm = document.getElementById('dragme'); 
        dm.addEventListener('dragstart',this.drag_start,false); 
        document.body.addEventListener('dragover',this.drag_over,false); 
        document.body.addEventListener('drop',this.drop,false); 
    }, 
    drag_start(event) {
        var style = window.getComputedStyle(event.target, null);
        event.dataTransfer.setData("text/plain",
        (parseInt(style.getPropertyValue("left"),10) - event.clientX) + ',' + (parseInt(style.getPropertyValue("top"),10) - event.clientY));
    }, 
    drag_over(event) { 
        event.preventDefault(); 
        return false; 
    },
    drop(event) { 
        var offset = event.dataTransfer.getData("text/plain").split(',');
        var dm = document.getElementById('dragme');
        dm.style.left = (event.clientX + parseInt(offset[0],10)) + 'px';
        dm.style.top = (event.clientY + parseInt(offset[1],10)) + 'px';
        event.preventDefault();
        return false;
    } 
  }
}
</script>

<style>
    .modal {
        z-index:1000;
    }
    .player-comparing .player-card.winner, .player-comparing .opponent-card.winner {
        transform: translateY(-50%);
    }
    .player-comparing .player-card.loser, .player-comparing .opponent-card.loser {
        transform: translateY(50%);
        opacity: .3;
    }
    .trash {
        width: 3.7em;
        height: 5em;
        border: 1px solid #666;
        border-radius: .3em;
        font-size: 1.2em;
    }
    .trash .card {
        @apply transition top-0;
        position: absolute !important;
    }
    .moving-card.dealt-to-player {
        transform: translate(-430%, 510%);
    }
    .moving-card.dealt-to-north {
        transform: translate(-250%, -500%);
    }
    .moving-card.dealt-to-east {
        transform: translate(800%, 10%) rotate(90deg)
    }
    .moving-card.dealt-to-west {
        transform: translate(-1500%, 10%) rotate(90deg)
    }
    .popup-starttour .inside, .popup-infoTour .inside {
        @apply left-full transition;
    }
    .popup-starttour.open .inside, .popup-infoTour.open .inside{
        @apply left-1/2 -translate-x-1/2;
    }
    .popup-starttour, .popup-infoTour {
        pointer-events:none;
    }
    .bg-green {
        background: linear-gradient(to right, #73D01E, #69bd1b);
    }
    .menu {
        transform: translateY(-59%);
        opacity: 0.3;
    }
    .scores {
        opacity: 0.3;
        transform: translate(-80%, -10%);
    }
    .menu:hover, .scores:hover {
        opacity: 1;
    }
    .menu.active {
        transform: none;
        opacity: 1;
    }
    .scores.active {
        opacity: 1;
        transform: translate(0, -10%)
    }
    .menu .actions, .menu .arrow-down, .scores .data, .scores .arrow-right, .inside {
        background-color: rgb(27 87 138)
    }
    .menu .fake {
        transform: translateX(2px);
        height: 10px;
    }
    .menu .arrow-down {
        &::before {
            content: '';
            display: inline-block;
            width: 0;
            height: 0;
            border-style: solid;
            border-width: 20px 10px 0 10px;
            border-color: black transparent transparent transparent;
        }
    }
    .scores .arrow-right {
        &::before {
            content: '';
            display: inline-block;
            width: 0;
            height: 0;
            border-style: solid;
            border-width: 10px 0 10px 20px;
            border-color: transparent transparent transparent black;
        }
    }
    .bg-gray {
        background: linear-gradient(to bottom, #5b5c5e, #3b3c3d);
    }
    aside { 
        position:  absolute;
        left: 0;
        top: 0; /* set these so Chrome doesn't return 'auto' from getComputedStyle */
        width: 200px; 
        height: 250px;
        background: rgba(255,255,255,0.66); 
        border: 2px  solid rgba(0,0,0,0.5); 
        border-radius: 4px; padding: 8px;
    }
    .camera {
        width: 200px;
        height: 250px;
        border: 2px  solid rgba(0,0,0,0.5); 
        border-radius: 4px;
        z-index:49;
    }
    .board {
        /* background: linear-gradient(to bottom, #d1b159, #dbca9a); */
        background: url('/welcome-on-board/dist/floor.png');
        @apply bg-contain;
    }
    .cards {

    }

        /**
    * Styles for CSS Playing Cards
    *
    * @author   Anika Henke <anika@selfthinker.org>
    * @license  CC BY-SA [http://creativecommons.org/licenses/by-sa/3.0]
    * @version  2011-06-14
    * @link     http://selfthinker.github.com/CSS-Playing-Cards/
    */

    /* card itself
    ********************************************************************/

    .playingCards .card {
        display: inline-block;
        width: 3.3em;
        height: 4.6em;
        border: 1px solid #666;
        border-radius: .3em;
        -moz-border-radius: .3em;
        -webkit-border-radius: .3em;
        -khtml-border-radius: .3em;
        margin: 0 .5em .5em 0;
        text-align: center;
        font-size: 1.2em; /* @change: adjust this value to make bigger or smaller cards */
        font-weight: normal;
        font-family: Arial, sans-serif;
        position: relative;
        background-color: #fff;
        -moz-box-shadow: .2em .2em .5em #333;
        -webkit-box-shadow: .2em .2em .5em #333;
        box-shadow: .2em .2em .5em #333;
    }
    .playingCards .hand .card {
        border: none;
    }

    .playingCards a.card {
        text-decoration: none;
    }
    .card .value {
        width: 30px;
        height: 30px;
        @apply flex justify-center items-center;
    }
    .playingCards ul.hand .card:hover, .playingCards ul.hand .card.selected {
        width: 170px;
        height: auto;
        transform: translateY(-20%);
        z-index: 1000;
        box-shadow: 0px 0px 10px 10px rgb(170, 37, 37);
    }
    .playingCards ul.hand .card.selected.played {
        transform: translateY(-50%)
    }
    .playingCards ul.hand .card:hover .rank {
        @apply mt-2;
    }
    .playingCards ul.hand .card:hover .value {
        width: 50px;
        height: 50px;
    }
    .playingCards ul.hand .card:hover .desc {
        display: block;
    }
    /* selected and hover state */
    .playingCards a.card:hover, .playingCards a.card:active, .playingCards a.card:focus,
    .playingCards label.card:hover,
    .playingCards strong .card {
        bottom: 1em;
    }
    .playingCards label.card {
        cursor: pointer;
    }

    .playingCards .card.back {
        text-indent: -4000px;
        background-color: #ccc;
        background-repeat: repeat;
        background-image: url(~assets/back-card.png); /* image is "Pattern 069" from http://www.squidfingers.com/patterns/ */

        /* yes, it's intentional that Mozilla, Webkit, Opera and IE all will get different backgrounds ... why not? :) */
    }

    /* suit colours
    ********************************************************************/

    .playingCards .card.diams {
        color: #f00 !important;
    }
    .playingCards.fourColours .card.diams {
        color: #00f !important;
    }
    [lang=de] .playingCards.fourColours .card.diams {
        color: #f60 !important;
    }
    .playingCards .card.hearts {
        color: #f00 !important;
    }
    .playingCards .card.spades {
        color: #000 !important;
    }
    [lang=de] .playingCards.fourColours .card.spades {
        color: #090 !important;
    }
    .playingCards .card.clubs {
        color: #000 !important;
    }
    .playingCards.fourColours .card.clubs {
        color: #090 !important;
    }
    [lang=de] .playingCards.fourColours .card.clubs {
        color: #000 !important;
    }
    .playingCards .card.joker {
        color: #000 !important;
    }
    .playingCards .card.joker.big {
        color: #f00 !important;
    }

    /* inner bits
    ********************************************************************/

    /* top left main info (rank and suit) */

    .playingCards .card .rank,
    .playingCards .card .suit {
        display: block;
        line-height: 1;
    }
    .playingCards .card .rank {
        @apply text-center;
    }
    .playingCards .card .suit {
        line-height: .7;
    }

    /* checkbox */
    .playingCards .card input {
        margin-top: -.05em;
        font: inherit;
    }
    .playingCards.simpleCards .card input,
    .playingCards .card.rank-j input,
    .playingCards .card.rank-q input,
    .playingCards .card.rank-k input,
    .playingCards .card.rank-a input {
        margin-top: 2.4em;
    }
    .playingCards.inText .card input {
        margin-top: 0;
    }

    /* different rank letters for different languages */
    .playingCards .card .rank:after,
    .playingCards .card.joker .rank:before {
        position: absolute;
        top: .25em;
        left: .25em;
        background: #fff;
    }
    [lang=de] .playingCards .card.rank-j .rank:after {
        content: "B";
    }
    [lang=fr] .playingCards .card.rank-j .rank:after {
        content: "V";
    }
    [lang=de] .playingCards .card.rank-q .rank:after,
    [lang=fr] .playingCards .card.rank-q .rank:after {
        content: "D";
    }
    [lang=fr] .playingCards .card.rank-k .rank:after {
        content: "R";
    }

    /* joker (top left symbol) */
    .playingCards .card.joker .rank {
        position: absolute;
    }
    .playingCards .card.joker .rank:before {
        content: "\2605";
        top: 0;
        left: 0;
    }
    .playingCards .card.joker .suit {
        text-indent: -9999px;
    }

    /* inner multiple suits */
    .playingCards .card .suit:after {
        display: block;
        margin-top: -.8em;
        text-align: center;
        white-space: pre;
        line-height: .9;
        font-size: 1.3em;
        word-spacing: -.05em;
    }

    /* make the hearts and clubs symbols fit, because they are a bit bigger than the others */
    .playingCards .card.hearts .suit:after {
        word-spacing: -.15em;
    }
    .playingCards .card.hearts.rank-10 .suit:after {
        word-spacing: -.05em;
        letter-spacing: -.1em;
    }
    .playingCards .card.clubs.rank-10 .suit:after {
        word-spacing: -.15em;
    }

    /* 8, 9, 10 are the most crowded */
    .playingCards .card.rank-8 .suit:after,
    .playingCards .card.rank-9 .suit:after {
        letter-spacing: -.075em;
    }
    .playingCards .card.rank-10 .suit:after {
        letter-spacing: -.1em;
    }
    .playingCards .card.clubs .suit:after {
        letter-spacing: -.125em;
    }

    /*____________ symbols in the middle (suits, full) ____________*/

    /* diamonds */
    .playingCards .card.rank-2.diams .suit:after {
        content: "\2666 \A\A\2666";
    }
    .playingCards .card.rank-3.diams .suit:after {
        content: "\2666 \A\2666 \A\2666";
    }
    .playingCards .card.rank-4.diams .suit:after {
        content: "\2666\00A0\00A0\00A0\2666 \A\A\2666\00A0\00A0\00A0\2666";
    }
    .playingCards .card.rank-5.diams .suit:after {
        content: "\2666\00A0\00A0\00A0\2666 \A\2666 \A\2666\00A0\00A0\00A0\2666";
    }
    .playingCards .card.rank-6.diams .suit:after {
        content: "\2666\00A0\00A0\00A0\2666 \A\2666\00A0\00A0\00A0\2666 \A\2666\00A0\00A0\00A0\2666";
    }
    .playingCards .card.rank-7.diams .suit:after {
        content: "\2666\00A0\00A0\2666 \A\2666\00A0\2666\00A0\2666 \A\2666\00A0\00A0\2666";
    }
    .playingCards .card.rank-8.diams .suit:after {
        content: "\2666\00A0\2666\00A0\2666 \A\2666\00A0\00A0\2666 \A\2666\00A0\2666\00A0\2666";
    }
    .playingCards .card.rank-9.diams .suit:after {
        content: "\2666\00A0\2666\00A0\2666 \A\2666\00A0\2666\00A0\2666 \A\2666\00A0\2666\00A0\2666";
    }
    .playingCards .card.rank-10.diams .suit:after {
        content: "\2666\00A0\2666\00A0\2666 \A\2666\00A0\2666\00A0\2666\00A0\2666 \A\2666\00A0\2666\00A0\2666";
    }

    /* hearts */
    .playingCards .card.rank-2.hearts .suit:after {
        content: "\2665 \A\A\2665";
    }
    .playingCards .card.rank-3.hearts .suit:after {
        content: "\2665 \A\2665 \A\2665";
    }
    .playingCards .card.rank-4.hearts .suit:after {
        content: "\2665\00A0\00A0\00A0\2665 \A\A\2665\00A0\00A0\00A0\2665";
    }
    .playingCards .card.rank-5.hearts .suit:after {
        content: "\2665\00A0\00A0\00A0\2665 \A\2665 \A\2665\00A0\00A0\00A0\2665";
    }
    .playingCards .card.rank-6.hearts .suit:after {
        content: "\2665\00A0\00A0\00A0\2665 \A\2665\00A0\00A0\00A0\2665 \A\2665\00A0\00A0\00A0\2665";
    }
    .playingCards .card.rank-7.hearts .suit:after {
        content: "\2665\00A0\00A0\2665 \A\2665\00A0\2665\00A0\2665 \A\2665\00A0\00A0\2665";
    }
    .playingCards .card.rank-8.hearts .suit:after {
        content: "\2665\00A0\2665\00A0\2665 \A\2665\00A0\00A0\2665 \A\2665\00A0\2665\00A0\2665";
    }
    .playingCards .card.rank-9.hearts .suit:after {
        content: "\2665\00A0\2665\00A0\2665 \A\2665\00A0\2665\00A0\2665 \A\2665\00A0\2665\00A0\2665";
    }
    .playingCards .card.rank-10.hearts .suit:after {
        content: "\2665\00A0\2665\00A0\2665 \A\2665\00A0\2665\00A0\2665\00A0\2665 \A\2665\00A0\2665\00A0\2665";
    }

    /* spades */
    .playingCards .card.rank-2.spades .suit:after {
        content: "\2660 \A\A\2660";
    }
    .playingCards .card.rank-3.spades .suit:after {
        content: "\2660 \A\2660 \A\2660";
    }
    .playingCards .card.rank-4.spades .suit:after {
        content: "\2660\00A0\00A0\00A0\2660 \A\A\2660\00A0\00A0\00A0\2660";
    }
    .playingCards .card.rank-5.spades .suit:after {
        content: "\2660\00A0\00A0\00A0\2660 \A\2660 \A\2660\00A0\00A0\00A0\2660";
    }
    .playingCards .card.rank-6.spades .suit:after {
        content: "\2660\00A0\00A0\00A0\2660 \A\2660\00A0\00A0\00A0\2660 \A\2660\00A0\00A0\00A0\2660";
    }
    .playingCards .card.rank-7.spades .suit:after {
        content: "\2660\00A0\00A0\2660 \A\2660\00A0\2660\00A0\2660 \A\2660\00A0\00A0\2660";
    }
    .playingCards .card.rank-8.spades .suit:after {
        content: "\2660\00A0\2660\00A0\2660 \A\2660\00A0\00A0\2660 \A\2660\00A0\2660\00A0\2660";
    }
    .playingCards .card.rank-9.spades .suit:after {
        content: "\2660\00A0\2660\00A0\2660 \A\2660\00A0\2660\00A0\2660 \A\2660\00A0\2660\00A0\2660";
    }
    .playingCards .card.rank-10.spades .suit:after {
        content: "\2660\00A0\2660\00A0\2660 \A\2660\00A0\2660\00A0\2660\00A0\2660 \A\2660\00A0\2660\00A0\2660";
    }

    /* clubs */
    .playingCards .card.rank-2.clubs .suit:after {
        content: "\2663 \A\A\2663";
    }
    .playingCards .card.rank-3.clubs .suit:after {
        content: "\2663 \A\2663 \A\2663";
    }
    .playingCards .card.rank-4.clubs .suit:after {
        content: "\2663\00A0\00A0\00A0\2663 \A\A\2663\00A0\00A0\00A0\2663";
    }
    .playingCards .card.rank-5.clubs .suit:after {
        content: "\2663\00A0\00A0\00A0\2663 \A\2663 \A\2663\00A0\00A0\00A0\2663";
    }
    .playingCards .card.rank-6.clubs .suit:after {
        content: "\2663\00A0\00A0\00A0\2663 \A\2663\00A0\00A0\00A0\2663 \A\2663\00A0\00A0\00A0\2663";
    }
    .playingCards .card.rank-7.clubs .suit:after {
        content: "\2663\00A0\00A0\2663 \A\2663\00A0\2663\00A0\2663 \A\2663\00A0\00A0\2663";
    }
    .playingCards .card.rank-8.clubs .suit:after {
        content: "\2663\00A0\2663\00A0\2663 \A\2663\00A0\00A0\2663 \A\2663\00A0\2663\00A0\2663";
    }
    .playingCards .card.rank-9.clubs .suit:after {
        content: "\2663\00A0\2663\00A0\2663 \A\2663\00A0\2663\00A0\2663 \A\2663\00A0\2663\00A0\2663";
    }
    .playingCards .card.rank-10.clubs .suit:after {
        content: "\2663\00A0\2663\00A0\2663 \A\2663\00A0\2663\00A0\2663\00A0\2663 \A\2663\00A0\2663\00A0\2663";
    }

    /*____________ symbols in the middle (faces as images) ____________*/

    .playingCards.faceImages .card.rank-j .suit:after,
    .playingCards.faceImages .card.rank-q .suit:after,
    .playingCards.faceImages .card.rank-k .suit:after {
        content: '';
    }
    .playingCards.faceImages .card.rank-j,
    .playingCards.faceImages .card.rank-q,
    .playingCards.faceImages .card.rank-k,
    .playingCards.faceImages .card.joker {
        background-repeat: no-repeat;
        background-position: -1em 0;
        /* @change: smaller cards: more negative distance from the left
        bigger cards: 0 or more positive distance from the left */

        /* for a centered full background image:
        background-position: .35em 0;
        -moz-background-size: contain;
        -o-background-size: contain;
        -webkit-background-size: contain;
        -khtml-background-size: contain;
        background-size: contain;
        */
    }

    
    .playingCards.simpleCards .card.rank-j,
    .playingCards.simpleCards .card.rank-q,
    .playingCards.simpleCards .card.rank-k       { background-image: none !important; }

    /*____________ symbols in the middle (faces as dingbat symbols) ____________*/

    .playingCards.simpleCards .card .suit:after,
    .playingCards .card.rank-j .suit:after,
    .playingCards .card.rank-q .suit:after,
    .playingCards .card.rank-k .suit:after,
    .playingCards .card.rank-a .suit:after,
    .playingCards .card.joker .rank:after {
        font-family: Georgia, serif;
        position: absolute;
        font-size: 3em;
        right: .1em;
        bottom: .25em;
        word-spacing: normal;
        letter-spacing: normal;
        line-height: 1;
    }
    .playingCards .card.rank-j .suit:after {
        content: "\265F";
        right: .15em;
    }
    .playingCards .card.rank-q .suit:after {
        content: "\265B";
    }
    .playingCards .card.rank-k .suit:after {
        content: "\265A";
    }
    /* joker (inner symbol) */
    .playingCards.faceImages .card.joker .rank:after {
        content: "";
    }
    .playingCards .card.joker .rank:after {
        position: absolute;
        content: "\2766";
        top: .4em;
        left: .1em;
    }

    /* big suits in middle */
    .playingCards.simpleCards .card .suit:after,
    .playingCards .card.rank-a .suit:after {
        font-family: Arial, sans-serif;
        line-height: .9;
        bottom: .35em;
    }
    .playingCards.simpleCards .card.diams .suit:after,
    .playingCards .card.rank-a.diams .suit:after {
        content: "\2666";
        right: .4em;
    }
    .playingCards.simpleCards .card.hearts .suit:after,
    .playingCards .card.rank-a.hearts .suit:after {
        content: "\2665";
        right: .35em;
    }
    .playingCards.simpleCards .card.spades .suit:after,
    .playingCards .card.rank-a.spades .suit:after {
        content: "\2660";
        right: .35em;
    }
    .playingCards.simpleCards .card.clubs .suit:after,
    .playingCards .card.rank-a.clubs .suit:after {
        content: "\2663";
        right: .3em;
    }

    /*____________ smaller cards for use inside text ____________*/

    .playingCards.inText .card {
        font-size: .4em;
        vertical-align: middle;
    }
    .playingCards.inText .card span.rank,
    .playingCards.inText .card span.suit {
        text-align: center;
    }
    .playingCards.inText .card span.rank {
        font-size: 2em;
        margin-top: .2em;
    }
    .playingCards.inText .card span.suit {
        font-size: 2.5em;
    }
    .playingCards.inText .card .suit:after,
    .playingCards.inText .card.joker .rank:after {
        content: "" !important;
    }
    .playingCards.inText .card .rank:after {
        left: .5em;
        padding: 0 .1em;
    }


    /* hand (in your hand or on table or as a deck)
    ********************************************************************/

    .playingCards ul.table,
    .playingCards ul.hand,
    .playingCards ul.deck {
        list-style-type: none;
        padding: 0;
        margin: 0 0 1.5em 0;
        clear: both;
    }
    .playingCards ul.hand {
        margin-bottom: 3.5em;
    }
    .playingCards ul.hand .card {
        width: 110px;
        height: 150px;
        transition: 0.3s;
        background: linear-gradient(to right, #d1b159, #dbca9a);
    }
    .playingCards ul.hand .card .desc {
        display: none;
    }
    .playingCards ul.table li,
    .playingCards ul.hand li,
    .playingCards ul.deck li {
        margin: 0;
        padding: 0;
        list-style-type: none;
        float: left;
    }

    .playingCards ul.hand,
    .playingCards ul.deck {
        height: 8em;
    }
    .playingCards ul.hand li,
    .playingCards ul.deck li {
        position: absolute;
    }
    .playingCards ul.hand li {
        bottom: 0;
    }
    .playingCards ul.hand li:nth-child(1)  { left: 0; }
    .playingCards ul.hand li:nth-child(2)  { left: 8.1em; }
    .playingCards ul.hand li:nth-child(3)  { left: 2.2em; }
    .playingCards ul.hand li:nth-child(4)  { left: 3.3em; }
    .playingCards ul.hand li:nth-child(5)  { left: 4.4em; }
    .playingCards ul.hand li:nth-child(6)  { left: 5.5em; }
    .playingCards ul.hand li:nth-child(7)  { left: 6.6em; }
    .playingCards ul.hand li:nth-child(8)  { left: 7.7em; }
    .playingCards ul.hand li:nth-child(9)  { left: 8.8em; }
    .playingCards ul.hand li:nth-child(10) { left: 9.9em; }
    .playingCards ul.hand li:nth-child(11) { left: 11em; }
    .playingCards ul.hand li:nth-child(12) { left: 12.1em; }
    .playingCards ul.hand li:nth-child(13) { left: 13.2em; }

    .playingCards ul.hand li:nth-child(14) { left: 14.3em; }
    .playingCards ul.hand li:nth-child(15) { left: 15.4em; }
    .playingCards ul.hand li:nth-child(16) { left: 16.5em; }
    .playingCards ul.hand li:nth-child(17) { left: 17.6em; }
    .playingCards ul.hand li:nth-child(18) { left: 18.7em; }
    .playingCards ul.hand li:nth-child(19) { left: 19.8em; }
    .playingCards ul.hand li:nth-child(20) { left: 20.9em; }
    .playingCards ul.hand li:nth-child(21) { left: 22em; }
    .playingCards ul.hand li:nth-child(22) { left: 23.1em; }
    .playingCards ul.hand li:nth-child(23) { left: 24.2em; }
    .playingCards ul.hand li:nth-child(24) { left: 25.3em; }
    .playingCards ul.hand li:nth-child(25) { left: 26.4em; }
    .playingCards ul.hand li:nth-child(26) { left: 27.5em; }

    /* rotate cards if rotateHand option is on */
    .playingCards.rotateHand ul.hand li:nth-child(1) {
        -moz-transform: translate(1.9em, .9em) rotate(-42deg);
        -webkit-transform: translate(1.9em, .9em) rotate(-42deg);
        -o-transform: translate(1.9em, .9em) rotate(-42deg);
        transform: translate(1.9em, .9em) rotate(-42deg);
    }
    .playingCards.rotateHand ul.hand li:nth-child(2) {
        -moz-transform: translate(1.5em, .5em) rotate(-33deg);
        -webkit-transform: translate(1.5em, .5em) rotate(-33deg);
        -o-transform: translate(1.5em, .5em) rotate(-33deg);
        transform: translate(1.5em, .5em) rotate(-33deg);
    }
    .playingCards.rotateHand ul.hand li:nth-child(3) {
        -moz-transform: translate(1.1em, .3em) rotate(-24deg);
        -webkit-transform: translate(1.1em, .3em) rotate(-24deg);
        -o-transform: translate(1.1em, .3em) rotate(-24deg);
        transform: translate(1.1em, .3em) rotate(-24deg);
    }
    .playingCards.rotateHand ul.hand li:nth-child(4) {
        -moz-transform: translate(.7em, .2em) rotate(-15deg);
        -webkit-transform: translate(.7em, .2em) rotate(-15deg);
        -o-transform: translate(.7em, .2em) rotate(-15deg);
        transform: translate(.7em, .2em) rotate(-15deg);
    }
    .playingCards.rotateHand ul.hand li:nth-child(5) {
        -moz-transform: translate(.3em, .1em) rotate(-6deg);
        -webkit-transform: translate(.3em, .1em) rotate(-6deg);
        -o-transform: translate(.3em, .1em) rotate(-6deg);
        transform: translate(.3em, .1em) rotate(-6deg);
    }
    .playingCards.rotateHand ul.hand li:nth-child(6) {
        -moz-transform: translate(-.1em, .1em) rotate(3deg);
        -webkit-transform: translate(-.1em, .1em) rotate(3deg);
        -o-transform: translate(-.1em, .1em) rotate(3deg);
        transform: translate(-.1em, .1em) rotate(3deg);
    }
    .playingCards.rotateHand ul.hand li:nth-child(7) {
        -moz-transform: translate(-.5em, .2em) rotate(12deg);
        -webkit-transform: translate(-.5em, .2em) rotate(12deg);
        -o-transform: translate(-.5em, .2em) rotate(12deg);
        transform: translate(-.5em, .2em) rotate(12deg);
    }
    .playingCards.rotateHand ul.hand li:nth-child(8) {
        -moz-transform: translate(-.9em, .3em) rotate(21deg);
        -webkit-transform: translate(-.9em, .3em) rotate(21deg);
        -o-transform: translate(-.9em, .3em) rotate(21deg);
        transform: translate(-.9em, .3em) rotate(21deg);
    }
    .playingCards.rotateHand ul.hand li:nth-child(9) {
        -moz-transform: translate(-1.3em, .6em) rotate(30deg);
        -webkit-transform: translate(-1.3em, .6em) rotate(30deg);
        -o-transform: translate(-1.3em, .6em) rotate(30deg);
        transform: translate(-1.3em, .6em) rotate(30deg);
    }
    .playingCards.rotateHand ul.hand li:nth-child(10) {
        -moz-transform: translate(-1.7em, 1em) rotate(39deg);
        -webkit-transform: translate(-1.7em, 1em) rotate(39deg);
        -o-transform: translate(-1.7em, 1em) rotate(39deg);
        transform: translate(-1.7em, 1em) rotate(39deg);
    }
    .playingCards.rotateHand ul.hand li:nth-child(11) {
        -moz-transform: translate(-2.2em, 1.5em) rotate(48deg);
        -webkit-transform: translate(-2.2em, 1.5em) rotate(48deg);
        -o-transform: translate(-2.2em, 1.5em) rotate(48deg);
        transform: translate(-2.2em, 1.5em) rotate(48deg);
    }
    .playingCards.rotateHand ul.hand li:nth-child(12) {
        -moz-transform: translate(-2.8em, 2.1em) rotate(57deg);
        -webkit-transform: translate(-2.8em, 2.1em) rotate(57deg);
        -o-transform: translate(-2.8em, 2.1em) rotate(57deg);
        transform: translate(-2.8em, 2.1em) rotate(57deg);
    }
    .playingCards.rotateHand ul.hand li:nth-child(13) {
        -moz-transform: translate(-3.5em, 2.8em) rotate(66deg);
        -webkit-transform: translate(-3.5em, 2.8em) rotate(66deg);
        -o-transform: translate(-3.5em, 2.8em) rotate(66deg);
        transform: translate(-3.5em, 2.8em) rotate(66deg);
    }

    /* deck */
    .playingCards ul.deck li:nth-child(1)  { left: 0;    bottom: 0; }
    .playingCards ul.deck li:nth-child(2)  { left: 2px;  bottom: 1px; }
    .playingCards ul.deck li:nth-child(3)  { left: 4px;  bottom: 2px; }
    .playingCards ul.deck li:nth-child(4)  { left: 6px;  bottom: 3px; }
    .playingCards ul.deck li:nth-child(5)  { left: 8px;  bottom: 4px; }
    .playingCards ul.deck li:nth-child(6)  { left: 10px; bottom: 5px; }
    .playingCards ul.deck li:nth-child(7)  { left: 12px; bottom: 6px; }
    .playingCards ul.deck li:nth-child(8)  { left: 14px; bottom: 7px; }
    .playingCards ul.deck li:nth-child(9)  { left: 16px; bottom: 8px; }
    .playingCards ul.deck li:nth-child(10) { left: 18px; bottom: 9px; }
    .playingCards ul.deck li:nth-child(11) { left: 20px; bottom: 10px; }
    .playingCards ul.deck li:nth-child(12) { left: 22px; bottom: 11px; }
    .playingCards ul.deck li:nth-child(13) { left: 24px; bottom: 12px; }
    .playingCards ul.deck li:nth-child(14) { left: 26px; bottom: 13px; }
    .playingCards ul.deck li:nth-child(15) { left: 28px; bottom: 14px; }
    .playingCards ul.deck li:nth-child(16) { left: 30px; bottom: 15px; }
    .playingCards ul.deck li:nth-child(17) { left: 32px; bottom: 16px; }
    .playingCards ul.deck li:nth-child(18) { left: 34px; bottom: 17px; }
    .playingCards ul.deck li:nth-child(19) { left: 36px; bottom: 18px; }
    .playingCards ul.deck li:nth-child(20) { left: 38px; bottom: 19px; }
    .playingCards ul.deck li:nth-child(21) { left: 40px; bottom: 20px; }
    .playingCards ul.deck li:nth-child(22) { left: 42px; bottom: 21px; }
    .playingCards ul.deck li:nth-child(23) { left: 44px; bottom: 22px; }
    .playingCards ul.deck li:nth-child(24) { left: 46px; bottom: 23px; }
    .playingCards ul.deck li:nth-child(25) { left: 48px; bottom: 24px; }
    .playingCards ul.deck li:nth-child(26) { left: 50px; bottom: 25px; }
    .playingCards ul.deck li:nth-child(27) { left: 52px; bottom: 26px; }
    .playingCards ul.deck li:nth-child(28) { left: 54px; bottom: 27px; }
    .playingCards ul.deck li:nth-child(29) { left: 56px; bottom: 28px; }
    .playingCards ul.deck li:nth-child(30) { left: 58px; bottom: 29px; }
    .playingCards ul.deck li:nth-child(31) { left: 60px; bottom: 30px; }
    .playingCards ul.deck li:nth-child(32) { left: 62px; bottom: 31px; }
</style>
