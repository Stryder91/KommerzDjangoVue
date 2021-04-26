<template>
    <div class="page-log-in">
        <div class="columns">
            <div class="column is-4 is-offset-4">
                <h1 class="title">Log In</h1>

                <form @submit.prevent="submitForm">
                    <div class="field">
                        <label for="username">Username</label>
                        <div class="control">
                            <input v-model="username" type="text" class="input" id="username">
                        </div>
                    </div>

                    <div class="field">
                        <label for="password">Password</label>
                        <div class="control">
                            <input v-model="password" type="password" class="input" id="password">
                        </div>
                    </div>

                    <div class="notification is-danger" v-if="errors.length">
                        <p v-for="error in errors" v-bind:key="error">{{ error }}</p>
                    </div>

                    <div class="field">
                        <div class="control">
                            <button class="button is-dark">Log In</button>
                        </div>
                    </div>

                    <hr>

                    Or <router-link to="/sign-up">Doesn't have an account yet ?</router-link>
                </form>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
// import { toast } from 'bulma-toast';

export default {
    name: 'Login',
    data() {
        return {
            username: '',
            password: '',
            errors: []
        }
    },
    mounted() {
        document.title = "Log In | Meihua Shop"
    },
    methods: {
        async submitForm() {

            axios.defaults.headers.common["Authorization"] = ""
            localStorage.removeItem('token');


            this.errors = [];

            if (this.username === "") {
                this.errors.push('The username is missing');
            }

            if(!this.errors.length) {
                const formData = {
                    username: this.username,
                    password: this.password
                };

                await axios.post('/api/v1/token/login/', formData)
                .then(res => {

                    const token = res.data.auth_token;

                    this.$store.commit('setToken', token);

                    axios.defaults.headers.common["Authorization"] = "Token " + token;

                    localStorage.setItem("token", token);

                    // Sert à revenir sur la page sur laquelle on était après loggé
                    const toPath = this.$route.query.to || '/cart'

                    this.$router.push(toPath);

                    // toast({
                    //     message: 'Account created, please login!',
                    //     type: 'is-success',
                    //     dismissible:true,
                    //     pauseOnHover: true,
                    //     duration: 2000,
                    //     position: 'bottom-right'
                    // });

                })
                .catch(err => {
                    if (err.response) {
                        for (const property in err.response.data) {
                            this.errors.push(`${property}: ${err.response.data[property]}`)
                        }
                        console.log(JSON.stringify(err.response.data));
                    } else if (err.message) {
                        this.errors.push('Something went wrong. Please try again')

                        console.log(JSON.stringify(err));
                    }
                })


            }
        }
    }
}
</script>