<template>
    <div id="app">
        <!-- String array example -->
        <Dropdown
            class="bordered"
            :options="options1"
            :selected="selected1"
            @setOption="selected1 = $event"
        />

        <!-- Object array example -->
        <Dropdown
            :options="options2"
            :selected="selected2"
            @setOption="selected2 = $event"
        />

        <!-- Dropdown menu example -->
        <Dropdown class="bordered">
            <template slot="dropdown-button">Menu</template>
            <template slot="dropdown-content">
                <a href="" class="dropdown-option">Link 1</a>
                <a href="" class="dropdown-option">Link 2</a>
                <a href="" class="dropdown-option">Link 3</a>
            </template>
        </Dropdown>
    </div>
</template>

<script>
import Dropdown from './components/Dropdown.vue';

export default {
    components: {
        Dropdown
    },

    data() {
        return {
            selected1: null,
            selected2: null,
            options1: ['Option 1', 'Option 2', 'Option 3', 'Option 4', 'Option 5'],
            options2: [
                { id: 1, name: 'Option 1' },
                { id: 2, name: 'Option 2' },
                { id: 3, name: 'Option 3' },
                { id: 4, name: 'Option 4 with a longer text to check the overflow' },
                { id: 5, name: 'Option 5' }
            ]
        }
    }
}
</script>

<style lang="scss">
*,
*::after,
*::before {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}
body {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
}
#app {
    display: flex;
    justify-content: center;
    text-align: center;
    margin-top: 60px;
}
.img {
    display: block;
    width: 300px;
    height: 300px;
}
</style>