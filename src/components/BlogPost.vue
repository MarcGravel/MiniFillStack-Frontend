<template>
    <div id='blogPosts'>
        <h2>Post ID: {{post[0]}}</h2>
        <p>{{post[1]}}</p>
        <div id="postBtns">
            <v-btn 
                id="editBtn" 
                @click="overlay = !overlay"
                >
                Edit
            </v-btn>
            <v-btn 
                id="deleteBtn" 
                @click="deletePost(post[0])"
                >
                Delete
            </v-btn> 
        </div> 
        <v-overlay
            :absolute="absolute"
            :value="overlay"
            :opacity="opacity">
            <v-form id="editPostForm">
                <h3>Edit the post here</h3>
                <v-text-field id="editInput"
                    v-model="updatedPostContent"
                    :label="post[1]"
                ></v-text-field>
                <div id="btnGrid">
                    <div id="saveAndBackBtns">
                        <v-btn id="saveBtn"
                            @click="sendUpdate(post[0], updatedPostContent), overlay = !overlay">
                            Save
                        </v-btn>
                        <v-btn id="overlayBackBtn"
                            @click="overlay = !overlay">
                                Back
                        </v-btn>
                    </div>
                </div>
            </v-form>
        </v-overlay>
    </div>
</template>

<script>
import axios from 'axios'
import { eventBus } from '../main'

    export default {
        name: "BlogPost",
        props: ['post'],
        data() {
            return {
                overlay: false,
                opacity: 0.9,
                absolute: true,
                updatedPostContent: "",
            }
        },
        methods: {
            sendUpdate(id, content) {
                axios.request({
                    url: 'http://127.0.0.1:5000/api/posts',
                    method: "PATCH",
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    data: {
                        "id": id,
                        "content": content
                    }
                }).then((response) => {
                    console.log(response);
                    eventBus.$emit('updateFeed');
                }).catch((error) => {
                    console.log(error);
                })
            },
            deletePost(postId) {
                axios.request({
                    url: 'http://127.0.0.1:5000/api/posts',
                    method: "DELETE",
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    data: {
                        "id": postId
                    }
                }).then((response) => {
                    console.log(response);
                    eventBus.$emit('updateFeed');
                }).catch((error) => {
                    console.log(error);
                })
            },
        }
    }
</script>

<style lang="scss" scoped>
    #blogPosts {
        overflow-y: auto;
        width: 50vw;
        height: fit-content;
        padding: 2vw 2vh 2vw 2vh;
        background-color: azure;
        border-radius: 25px;
        display: grid;

        h2 {
            color: rgb(156, 6, 6);
            margin-bottom: 1vh;
        }

        p {
            font-size: 1.3em;
            margin-bottom: 2vh;
        }

        #postBtns {
            width: fit-content;
            height: fit-content;
            justify-self: end;

            #editBtn {
                margin-right: 1vw;
                background-color: #24a0ed;
                color: white;
            }

            #deleteBtn {
                background-color: #C70000;
                color: white;
            }
        }

        #editPostForm {
            width: 50vw;

            #saveBtn {
                background-color: #24a0ed;
                color: white;
                margin-right: 1vw;
            }

            #overlayBackBtn {
                background-color: #C70000;
                color: white;
            }
        }
    }
</style>