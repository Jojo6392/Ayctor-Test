<template>
    <div id="app">
        <div class="content">
            <div class="title">
                <div class="hello">
                    Bonjour
                </div>
                <div class="name">
                    <!-- Version du nom de famille non coupé : {{ this.currentUser.name }} -->
                    <!-- Si on ne veut pas couper le nom de famille, enlever toutes les lignes en rapport avec des variables nommées "name2" dans le script.  -->
                    {{ this.currentUser.name2 }}
                </div>
            </div>

            <div class="list">
                Sélectionner un nouveau profil à l'aide de cette liste :
                <select v-model="currentUser.name">
                    <option v-for="user in users" :key="user.name">
                        {{ user.name }}
                    </option>
                </select>
            </div>

            <button @click="currentPicture = currentUser.img">
                Afficher sa photo de profil
            </button>
        </div>

        <img class="picture" :src="this.currentPicture" alt="profil picture">

        <img class="arrow" src="./assets/arrow.svg" alt="arrow">
    </div>
</template>

<script>
import axios from "axios"

export default {
    name: 'App',

    data() {
        return {
            users: [],
            currentUser: {
                name: null,
                name2: null,
                img: null,
            },

            currentPicture: require("./assets/hero-image.jpg"),
        }
    },

    watch: {
        "currentUser.name"(newValue) {
            const user = this.users.find(({ name }) => name === newValue)
            this.currentUser.name2 = user.name2
            this.currentUser.img = user.img
        }
    },

    mounted () {
        this.getUsers().then(() => {
            this.currentUser.name = this.users[0].name
            this.currentUser.name2 = this.users[0].name2
            this.currentUser.img = this.users[0].img
        });
    },

    methods: {
        async getUsers() {
            const reqUsers = await axios.get("https://randomuser.me/api/?results=20&nat=fr")
            const res = reqUsers.data.results
            res.forEach(user => {
                this.users.push({
                    name: user.name.first + " " + user.name.last,
                    name2: user.name.first + " " + user.name.last[0] + ".",
                    img: user.picture.large
                })
            });
        },
    },
}
</script>

<style lang="scss">

@font-face {
    font-family: "Georgia_Bold";
    src: url('./assets/police/GeorgiaPro-CondBold.ttf');
}

body {
    overflow: hidden;
}

#app {
    width: 100vw;
    height: 100vh;

    display: flex;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;
    gap: 150px;
}

.content {
    display: flex;
    flex-direction: column;
    gap: 25px;

    opacity: 0;
    animation: fadeInLeft 500ms forwards;
}

.title {
    font-family: "Georgia_Bold";
    color: rgb(77, 77, 77);
    font-size: 48px;
    
    .name {
        text-transform: uppercase;
    }
}

.list {
    font-family: 'Times New Roman';
    color: rgb(101, 101, 101);
    font-size: 18px;
}

button {
    all: unset;
    cursor: pointer;

    padding: 15px 35px;
    width: fit-content;

    color: white;
    background-color: #000;
    text-transform: uppercase;
    font-family: 'Arial';
    border-radius: 50px;

    &:hover, &:not(:hover) {
        transition: all ease 0.25s;
    }

    &:hover {
        transform: scale(1.1); 
    }

    &:not(:hover) {
        transform: scale(1);
    }
}

.picture {
    width: 650px;
    height: 650px;
    border-radius: 50%;
    box-shadow: 5px 5px 10px 5px rgb(240,240,240);

    opacity: 0;
    animation: zoomIn 500ms 250ms forwards;
}

.arrow {
    position: absolute;
    right: 0;
}

@media screen and (max-width: 1600px) {
    .arrow {
        right: -15%;
    }
}

@media screen and (max-width: 1200px) {
    .arrow {
        display: none;
    }
}

// ANIMATIONS

@keyframes fadeInLeft {
    from {
        opacity: 0;
        transform: translateX(-100%);
    }

    to {
        opacity: 1;
        transform: translateX(0%);
    }    
}

@keyframes zoomIn {
    from {
        opacity: 0;
        transform: scale(0);
    }

    to {
        opacity: 1;
        transform: scale(1);
    }
}

</style>
