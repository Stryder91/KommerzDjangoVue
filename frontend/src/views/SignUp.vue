<template>
    <div class="page-sign-up">
        <div class="columns">
            <div class="column is-4 is-offset-4">
                <h1 class="title">Sign Up</h1>

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

                    <div class="field">
                        <label for="repeat_password">Repeat Password</label>
                        <div class="control">
                            <input v-model="password2" type="password" class="input" id="repeat_password">
                        </div>
                    </div>

                    <div class="notification is-danger" v-if="errors.length">
                        <p v-for="error in errors" v-bind:key="error">{{ error }}</p>
                    </div>

                    <div class="field">
                        <div class="control">
                            <button class="button is-dark">Sign Up</button>
                        </div>
                    </div>

                    <hr>

                    Or <router-link to="/log-in">Already have an account ?</router-link>
                </form>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import { toast } from 'bulma-toast';

export default {
    name: 'SignUp',
    data() {
        return {
            username: '',
            password: '',
            password2: '',
            errors: []
        }
    },
    methods: {
        submitForm() {
            this.errors = [];

            if (this.username === "") {
                this.errors.push('The username is missing');
            }
            if (this.password !== this.password2) {
                this.errors.push('Password must match');   
            }
            if(!this.errors.length) {
                const formData = {
                    username: this.username,
                    password: this.password
                };

                axios.post('/api/v1/users/', formData)
                .then(res => {
                    toast({
                        message: 'Account created, please login!',
                        type: 'is-success',
                        dismissible:true,
                        pauseOnHover: true,
                        duration: 2000,
                        position: 'bottom-right'
                    });
                    console.log(res);
                    this.$router.push('/log-in');
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