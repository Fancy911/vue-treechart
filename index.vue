<template>
    <div id="tree-container">
        <tree-chart
            ref="treechart"
            :data="treedata"
            :children-key="'children'"
            :line-color="'#0006'"
            :line-width="2"
            :line-offset="20"
            :transition="0.3"
            :node-key="(item) => item.data.name"
        >
            <template slot-scope="{node}">
                <div class="my-tree-node" :style="{background: node.data.bgcolor}">
                    <span>{{ node.data.name }}</span>
                    <div>
                        <button
                            v-if="node.parent"
                            type="button"
                            @click="addSibling(node)"
                        >addSibling</button>
                        <button
                            v-if="node.parent"
                            type="button"
                            @click="remove(node)"
                        >remove</button>
                        <button
                            type="button"
                            @click="appendChild(node)"
                        >appendChild</button>
                    </div>
                </div>
            </template>
        </tree-chart>
    </div>
</template>

<script>
import TreeChart from './index.js';

/**
 * 处理原始数据，添加parent，index（当前父节点的第几个孩子）
 * @param {*} tree
 * @param {*} parent
 * @param {*} index
 */
function processTreeData(tree, parent = null, index = 0) {
    tree.parent = parent;
    tree.index = index;
    if (tree.children) {
        tree.children.forEach((child, _index) => {
            processTreeData(child, tree, _index);
        });
    }
    return tree;
}

// 初始树的数据
// 此处可以按照需求要求的数据格式进行调整
const testData = {
    isRoot: true,
    data: { name: 'root', bgcolor: '#1890ff'},
    children: [
        {
            data: { name: 'L1#1', bgcolor: '#1890ff'},
            children: [
                {
                    data: { name: 'L2#1 - ANJINSHUO', bgcolor: '#1890ff'},
                },
                {
                    data: {
                        name: 'L2#2 - nerfthisan@163.com',
                        bgcolor: '#f5222d',
                    },
                },
                {
                    data: {name: 'L2#3 - i58000 @ github', bgcolor: '#52c41a'},
                },
                {
                    data: {name: 'L2#4 - anjs @ npm', bgcolor: '#faad14'},
                },
            ],
        },
        {
            data: {
                name: 'L1#2-loooooooooooooong loooooooooooooong ago',
                bgcolor: '#1890ff',
            },
        },
        {
            data: {
                name: 'L1#3',
                bgcolor: '#1890ff',
                editable: true,
            },
            children: [
                {
                    data: {name: 'children 1', bgcolor: '#1890ff'},
                },
                {
                    data: {name: 'children 2', bgcolor: '#1890ff'},
                },
                {
                    data: {name: 'children 3', bgcolor: '#1890ff'},
                },
            ],
        },
    ],
};

let id = 1000;

export default {
    components: {
        TreeChart,
    },
    data() {
        return {
            treedata: processTreeData(testData),
        };
    },
    methods: {
        findNodeByName(node, nodeName) {
            if (node.name === nodeName) {
                return node;
            }
            if (node.children) {
                for (let i = 0; i < node.children.length; i++) {
                    const result = this.findNodeByName(node.children[i]);
                    if (result) {
                        return result;
                    }
                }
            }
            return null;
        },
        // 新增兄弟节点
        addSibling(node) {
            console.log(node);
            node.parent.children.push({
                children: [],
                data: {
                    bgcolor: '#ff0000',
                    name: `children ${++id}`,
                },
                parent: node.parent,
            });

            this.$refs.treechart.rerender();
        },
        // 删除节点
        remove(node) {
            node.parent.children.splice(node.index, 1);
            this.$refs.treechart.rerender();
        },
        // 新增子节点
        appendChild(node) {
            if (!node.children) {
                node.children = [];
            }
            node.children.push({
                children: [],
                data: {
                    bgcolor: '#ffff00',
                    name: `children ${++id}`,
                },
                parent: node,
            });
            this.$refs.treechart.rerender();
        },
    },
};
</script>
<style scoped>
.my-tree-node {
    margin: 10px;
    padding: 10px;
    border: 1px solid #d9d9d9;
    border-radius: 4px;
    background: #fff;
    color: #fff;
    box-shadow: 0 2px 8px rgba(0, 0, 0, .15);
    white-space: nowrap;
}

button {
    width: 90px;
    height: 20px;
    margin-left: 10px;
    border: none;
    border-radius: 4px;
    background: #fff;
    color: #333;
    outline: none;
    cursor: pointer;
}
</style>