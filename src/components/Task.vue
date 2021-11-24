<template>
    <div class="holder">
        <section>
            <h2>Pack Sizes</h2>

            <ul class="packs">
                <li v-for="(pack, index) in packs" :key="index">
                    <span>
                        {{ pack }}
                    </span>
                    <button v-on:click="remove(index)">
                        Delete
                    </button>
                </li>
            </ul>

            <h2>Add a Pack Size</h2>

            <form>

                <input type="number" ref="size" step="0.01"/>
                <button @click.prevent="addPackSize()">Add Pack Size</button>

            </form>
            

        </section>
        
        <section>

            <h2>Packs To Order</h2>

            <form>

                <input type="number" ref="amount" step="0.01" />
                <button @click.prevent="getPacks()">Submit</button>

            </form>

            <ul class="packs">
                <li>
                    <span>Pack Size:</span>
                    <span>Amount:</span>
                </li>
                <li v-for="(amount, pack, index) in order" :key="index" class=""> 
                    <span> {{ pack }} </span>
                    <span> {{ amount }} </span>
                </li>
            </ul>

        </section>
    </div>
    
</template>

<script>
export default {
    name: 'Task',

    data: function () {
        return {
            packs: [
                5000, 
                2000,
                1000, 
                500,
                250
            ],
            order: {
                
            }
        }
    },

    methods: {

        remove: function (index) {

            this.packs.splice(index, 1); // Remove element from the packs property
        },

        addPackSize: function () {

            if (parseInt(this.$refs.size.value, 10) && // Parse the input as an in to check if it's valid
                !this.packs.some(el => el === parseInt(this.$refs.size.value, 10)) // Check no existing element is already equal to the value you're trying to put in
            )
            {

                this.packs.push(parseInt(this.$refs.size.value, 10)); // Push the value in

                this.packs.sort((a, b) => { // Sort the pack sizes largest to smallest

                    return b - a;
                });
            }
        },

        getPacks: function () {

            this.order = {}; // Reset the order
            let amount = 0; // Reset the amount
            let current = 0; // Set the current amount
            let tempPacks = this.packs; // Create temporary mirror of the packs array


            if (parseInt(this.$refs.amount.value, 10)) { // Parse the amount inputted

                amount = parseInt(this.$refs.amount.value, 10); // Set the value

            } else {

                return; // Finish the function since the input was invalid
            }   

            while ((current < amount) && (tempPacks.length > 0)) { // While the current is less the the amount and the temp packs still has values loop

                let largest = Math.max(...tempPacks); // Get the largest pack

                if ((current + largest) > amount) { // If the current + the largest exceeds the amount

                    if (tempPacks.length == 1 && current < amount) { // Check if the largest is the only element in the temp packs 
                                                                    // If so means that it's the smallest available unit
                        this.checkAndCreateOrder(largest); // Check if the value exists, if not create it and set it to 0

                        this.order[largest] += 1; // Add one to the amount
                    }

                    tempPacks = tempPacks.filter( // Remove the largest from the temp packs
                        (value) => {
                            return value != largest
                        }
                    );

                } else { // Else the packs was a valid amount

                    this.checkAndCreateOrder(largest); // Check the value exists, if not create it

                    this.order[largest] += 1; // Add one to the value

                    current += largest; // Add value to the current
                }
            }

            Object.entries(this.order).forEach((set) => { // Check the orders to make sure that the amount of packs * size of packs doesn't equal another pack

                const [key, val] = set; // Get the key value pairs

                if (this.packs.some(el => el === (key * val))) { // If there is an equal amount

                    delete this.order[key]; // Remove the current one

                    this.checkAndCreateOrder(key * val); // Check that if value exists, if not create it

                    this.order[key * val] += 1; // Add one to the pack\

                }
            });   
        },

        checkAndCreateOrder: function (key) { // Common used function for checking if the order has a key

            if (!this.order[key]) { // If it doesn't have the key

                this.order[key] = 0; // Creates the key and sets it equal to 0
            }
        }
    }
}
</script>

<style scoped>
    h2 {
        margin-top: 0;
    }

    .holder {
        display: flex;
        flex-direction: row;
        margin: 0 auto;
        max-width: 50%;
    }

    section {
        border-radius: 20px;
        border: 1px solid #c9d1d9;
        box-sizing:border-box;
        display: inline-block;

        margin: 0 2%;
        padding: 30px;
    }

    .packs {
        display: flex;
        flex-direction: column;
        margin-top: 0px;
        margin-bottom: 30px;
        padding: 0;
    }

    .packs li {
        list-style: none;
        display: inline-flex;
        padding: 10px;

        justify-content: space-between;
    }

    .packs li:not(:last-child) {
        border-bottom: 1px solid #c9d1d9;
    }

    .packs li span {
        width: 50%;
    }

    input::-webkit-outer-spin-button,
    input::-webkit-inner-spin-button {
        -webkit-appearance: none;
        margin: 0;
    }

    input[type=number] {
        -moz-appearance: textfield;
    }
</style>
