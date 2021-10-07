<template>
    <div id="blogContainer">
        <div id="inputContainer">
            <h1>Submit a post</h1>
            <v-textarea id="inputArea"
                background-color="grey lighten-2"
                color="white"
                height="30vh"
                :rules="maxCharacters"
                filled
                counter
                maxlength="2000"
                label="Write your post here - Max 2000 characters"
                v-model="userInput"
            ></v-textarea>
            <v-btn 
                id="submitBtn"
                @click="uploadPost(userInput)"
                >
                Submit
            </v-btn>
        </div>
        <div id="displayContainer">
            <h1>All Posts</h1>
            <div id='blogPostDisplay' v-for="post in this.allPosts" :key="post.id">
                <BlogPost :post="post" />
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import BlogPost from '../components/BlogPost.vue'
import { eventBus } from '../main'

export default {
    name: "BlogPage",
    components: {
        BlogPost
    },
    beforeMount() {
        this.loadAllPosts()
    },
    updated() {
        eventBus.$on('updateFeed', () => {
                this.loadAllPosts();
            }) 
    },
    data () {
        return {
            maxCharacters: [
                (v) => !!v || "Input is required",
                (v) =>
                (v && v.length <= 1999) ||
                "Post must be less than 2000 characters",
            ],
            userInput: "",
            allPosts: {

            },
        }
    },
    methods: {
        loadAllPosts() {
            axios.request({
                url: 'http://127.0.0.1:5000/api/posts',
                method: "GET",
                headers: {
                    'Content-Type': 'application/json'
                },
            }).then((response) => {
                    //reverses the order so you can see newest posts first
                    this.allPosts = response.data.reverse();
                }).catch((error) => {
                    console.log(error);
                })
        },
        uploadPost(userInput) {
            // checks if there is any input and makes sure input is not just whitespace
            if (userInput != '' && userInput.trim().length) {
                axios.request({
                    url: 'http://127.0.0.1:5000/api/posts',
                    method: "POST",
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    data: {
                        "content": userInput.trim()
                    }
                }).then((response) => {
                    console.log(response);
                    this.loadAllPosts();
                }).catch((error) => {
                    console.log(error);
                })

                //sets input feild to empy again
                this.userInput = "";
            }
        }
    }
}
</script>

<style lang="scss" scoped>
    #blogContainer {
        width: 100vw;
        height: 100vh;
        background-color: rgb(104, 104, 104);
        display: grid;
        grid-template-columns: 40% 60%;
        align-items: center;
        justify-items: center;

        #inputContainer {
            grid-column: 1;
            width: 90%;
            height: 60%;
            background-color: rgb(143, 143, 143);
            border-radius: 40px;
            display: grid;
            grid-template-rows: 20% 60% 20%;

            h1 {
                font-size: 4em;
                color: whitesmoke;
                justify-self: center;
            }

            #submitBtn {
                background-color: #24a0ed;
                width: 80%;
                height: 60%;
                justify-self: center;
            }

        }

        #displayContainer {
            grid-column: 2;
            width: 90%;
            height: 90%;
            background-color: rgb(143, 143, 143);
            border-radius: 40px;
            display: grid;
            grid-template-rows: 10vh;
            justify-items: center;
            overflow-y: scroll;

            h1 {
                grid-row: 1;
                font-size: 4em;
                color: whitesmoke;
            }

            #blogPostDisplay {
                height: fit-content;
                margin: 1vh 0 1vh 0;
            }
        }
    }
</style>