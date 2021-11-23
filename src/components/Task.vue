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
            amount: 0,
            order: {
                
            }
        }
    },

    methods: {

        remove: function (index) {

            this.packs.splice(index, 1);
        },

        addPackSize: function () {

            if (parseInt(this.$refs.size.value, 10)) {

                this.packs.push(parseInt(this.$refs.size.value, 10));

                this.packs.sort((a, b) => {

                    return a - b;
                });
            }
        },

        getPacks: function () {

            this.order = {};

            if (parseInt(this.$refs.amount.value, 10)) {

                this.amount = parseInt(this.$refs.amount.value, 10);
            }

            let current = 0;
            let tempPacks = this.packs;

            while ((current < this.amount) && (tempPacks.length > 0)) {

                let largest = Math.max(...tempPacks);

                if ((current + largest) > this.amount) {

                    if (tempPacks.length == 1 && current < this.amount) {

                        if (!this.order[largest]) {

                            this.order[largest] = 0;
                        }

                        this.order[largest] += 1;
                    }

                    tempPacks = tempPacks.filter(
                        (value) => {
                            return value != largest
                        }
                    );

                } else {

                    if (!this.order[largest]) {

                        this.order[largest] = 0;
                    }

                    this.order[largest] += 1;

                    current += largest;
                }
            }

            Object.entries(this.order).forEach((set) => {

                const [key, val] = set;

                if (this.packs.some(el => el === (key * val))) {

                    delete this.order[key];

                    this.order[key * val] = 1;
                }
            });

                
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
