<template>
<setting-base>
    <div v-title>我的上传 - 用户设置 - {{config.title}}</div>
    <h3 class="ic-header">我的上传</h3>
    <div class="list">
        <div class="item" v-for="item in page.items" :key="item.id">
            <div class="wrap ic-paper round ic-z1">
                <img :src="staticUrl(item.key)" />
            </div>
        </div>
    </div>
</setting-base>
</template>

<style lang="scss" scoped>
.list {
    display: flex;
    flex-wrap: wrap;
    align-content: space-between;
    margin: 0 -5px; // 与横向向外突出白边相抵消

    .item {
        flex: 0 0 25%;

        .wrap {
            margin: 5px;
            display: flex;
            align-items: center;
            justify-content: center;
            // min-height: 190px;

            img {
                width: 100%;
            }
        }
    }
}
</style>

<script>
import { mapState, mapGetters } from 'vuex'
import api from '@/netapi.js'
import SettingBase from '../base/base.vue'

export default {
    data () {
        return {
            page: []
        }
    },
    computed: {
        ...mapState(['config']),
        ...mapGetters('user', ['basicRole'])
    },
    created: async function () {
        this.$store.commit('LOADING_INC', 1)
        await this.fetchData()
        this.$store.commit('LOADING_DEC', 1)
    },
    methods: {
        staticUrl: $.staticUrl,
        fetchData: async function () {
            this.$store.commit('LOADING_INC', 1)
            // let params = this.$route.query
            // this.page.curPage = params.page
            // let ret = await api.upload.token('user')

            let ret = await api.upload.list({
                'type_name.is': null
            }, 1, null, this.basicRole)

            if (ret.code === api.retcode.SUCCESS) {
                this.page = ret.data
            //     // let pageNumber = this.$route.query.page
            //     // if (pageNumber) this.commentPage = parseInt(pageNumber)
            } else {
                $.message_by_code(ret.code)
            }
            this.$store.commit('LOADING_DEC', 1)
        }
    },
    watch: {
        // 如果路由有变化，会再次执行该方法
        '$route': 'fetchData'
    },
    components: {
        SettingBase
    }
}
</script>
