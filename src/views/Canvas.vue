<template>
    <section class="min-h-screen min-w-screen bg-gray-800 p-4">
        <Node v-for="node in nodes" :key="node.id" :node="node" />
    </section>
</template>
<script setup>
import { ref } from 'vue'
import Node from '../components/Node.vue'

const nodes = ref([
    {
        id: 1,
        label: "Node name 1",
        description: "Node description 1",
        children: [
            {
                id: 2,
                label: "Node name 2",
                children: [
                    {
                        id: 3,
                        label: "Node name 3",
                        children: []
                    }
                ]
            }
        ]
    },
    {
        id: 4,
        label: "Node name 4",
        description: "Node description 4",
        children: []
    },
    {
        id: 5,
        label: "Node name 5",
        description: "Node description 5",
        children: [
            {
                id: 22,
                label: "Node name 22",
                description: "lalalala lalalala lalalala lalalala lalalala lalalala lalalala lalalala lalalala lalalala lalalala ",
                children: [
                    {
                        id: 3,
                        label: "Node name 3a",
                        children: []
                    }
                ]
            },
            {
                id: 2323,
                label: "Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 ",
                "description": "Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 Node name 23 ",
                children: [
                    {
                        id: 3,
                        label: "Node name 3f",
                        children: []
                    }
                ]
            },
            {
                id: 233,
                label: "Node name 23",
                children: [
                    {
                        id: 3,
                        label: "Node name 3f",
                        children: []
                    }
                ]
            },
            {
                id: 23,
                label: "Node name 23",
                children: [
                    {
                        id: 3,
                        label: "Node name 3f",
                        children: []
                    }
                ]
            }
        ]
    },
    {
        id: 6,
        label: "Node name 6",
        description: "Node description 6",
        children: []
    }
])

</script>
