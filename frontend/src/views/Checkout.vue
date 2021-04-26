<template>
    <div class="page-checkout">
        <div class="columns is-multiline">
            <div class="columns is-multiline">
                <div class="column is-12">
                    <h1 class="title">Checkout</h1>
                </div>

                <div class="column is-12 box">
                    <table class="table is-fullwidth">
                        <thead>
                            <tr>
                                <th>Product</th>
                                <th>Price</th>
                                <th>Quantity</th>
                                <th>Total</th>
                            </tr>
                        </thead>

                        <tbody>
                            <tr
                                v-for="item in cart.items"
                                v-bind:key="item.product.id"
                            >
                                <td>{{ item.product.name }}</td>
                                <td>${{ item.product.price }}</td>
                                <td>{{ item.quantity }}</td>
                                <td>${{ getItemTotal(item).toFixed(2) }}</td>
                            </tr>
                        </tbody>

                        <tfoot>
                            <tr>
                                <td colspan="2">Total</td>
                                <td>{{ cartTotalLength }}</td>
                                <td>${{ cartTotalLength.toFixed(2) }}</td>
                            </tr>
                        </tfoot>
                    </table>
                </div>

                <div class="column is-12 box">
                    <h2 class="subtitle">Shipping details</h2>
                    <p class="has-text-grey mb-4">* All fields are required</p>

                    <div class="columns is-multiline">
                        <div class="column is-6">

                            <div class="field">
                                <label>First name*</label>
                                <div class="control">
                                    <input v-model="first_name" type="text" class="input">
                                </div>
                            </div>

                            <div class="field">
                                <label>Last name*</label>
                                <div class="control">
                                    <input v-model="first_name" type="text" class="input">
                                </div>
                            </div>

                            <div class="field">
                                <label>Last name*</label>
                                <div class="control">
                                    <input v-model="last_name" type="text" class="input">
                                </div>
                            </div>

                             <div class="field">
                                <label>Email*</label>
                                <div class="control">
                                    <input v-model="email" type="email" class="input">
                                </div>
                            </div>

                            <div class="field">
                                <label>Phone*</label>
                                <div class="control">
                                    <input v-model="phone" type="text" class="input">
                                </div>
                            </div>
                        </div>  
                        <div class="column is-6">
                            <div class="field">
                                <label>Address*</label>
                                <div class="control">
                                    <input v-model="address" type="text" class="input">
                                </div>
                            </div>

                            <div class="field">
                                <label>ZipCode*</label>
                                <div class="control">
                                    <input v-model="zipcode" type="text" class="input">
                                </div>
                            </div>

                            <div class="field">
                                <label>Place*</label>
                                <div class="control">
                                    <input v-model="place" type="text" class="input">
                                </div>
                            </div>

                        </div>
                        <div class="notification is-danger mt-4" v-if="errors.length">
                            <p v-for="error in errors" v-bind:key="error"> {{ error }} </p>
                        </div>

                        <hr>

                        <div id="card-element" class="mb-5">
                        </div>
                        <template v-if="cartTotalLength"> 
                            <hr>
                            <button class="button is-dark" @click="submitForm">Pay with stripe</button>
                        </template>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
// import axios from 'axios'

export default {
    name: 'Checkout',
    data() {
        return {
            cart: {
                items: []
            },
            stripe: {},
            card: {},
            first_name: '',
            last_name: '',
            email: '',
            phone: '',
            address: '',
            zipcode: '',
            place: '',
            errors: []
        }
    },
    mounted() {
        document.title = "Checkout | Meihua Shop"

        this.cart = this.$store.state.cart
    },
    methods: {
        getItemTotal(item) {
            return item.quantity * item.product.price
        },
        submitForm() {
            this.errors = []
            if (this.first_name === '') {
                this.errors.push('The first name field is missing')
            }
            // Faire un switch
        }
    },
    computed: {
        cartTotalPrice() {
            return this.cart.items.reduce((acc, curVal) => {
                return acc += curVal.product.price * curVal.quantity
            }, 0)
        },
        cartTotalLength() {
            return this.cart.items.reduce((acc, curVal) => {
                return acc += curVal.quantity
            }, 0)
        },
        
    }    

}
</script>