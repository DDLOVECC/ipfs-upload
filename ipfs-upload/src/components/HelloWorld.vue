<template>
    <div class="all">
        <div class="title">
            <h1>
                Welcome to the ipfs server!
            </h1><br/>
            <h1> Image Url: <input v-model="imageOriginUrl"/></h1><br/>
            <h1>
                <button @click="storeNFT">storeNFT</button>
            </h1>
        </div>
    </div>
</template>
<script>
    import {NFTStorage, File} from 'nft.storage'
    import mime from 'mime'
    import {token} from '../key.js'
    export default {
        data() {
            return {
                imageOriginUrl: "", //商品主图：七牛图片url【例如：https://qiliu.keytocaring.com/Frh79tb8mRgJJwRsfQryZozYzPQP】
            }
        },
        methods: {
            async storeNFT() {
                //token 测试时需要到注册，注册账号创建key后，替换此处 token【注册流程说明：https://nft.storage/docs/#create-an-account】
                const nftstorage = new NFTStorage({token: token});
                const image = await this.getImage();
                //const imageFile = new File([image], '', {type: "image/*"})
                const nft = {
                    image: image, // use image Blob as `image` field
                    name: "Test Image", //真实场景为商品名称 【要求简短】 此处常量为了测试
                    description: "Image NFT",//真实场景为商品描述 【要求简短】 此处常量为了测试
                    properties: {
                        type: "image/*",
                        authors: [{name: "AI YUE"}],
                        content: {
                            "text/markdown": "NFT Content"
                        }
                    }
                }
                const metadata = await nftstorage.store(nft)
                console.log("metadata:", metadata)
                console.log('IPFS ipnft for the metadata:', metadata.ipnft)
                console.log('IPFS URL for the metadata:', metadata.url)
                console.log('gateway metadata url:', metadata.embed().image.origin + "/ipfs/" + metadata.ipnft + "/metadata.json")
               //最终metadata.json文件url 【此url即为每一个tokenId所对应url】
                console.log('gateway metadata url:', "https://ipfs.io/ipfs/" + metadata.ipnft + "/metadata.json")
            },
            async getImage() {
                //获取主图url
                const imageOriginUrl = this.imageOriginUrl;
                const r = await fetch(imageOriginUrl);
                if (!r.ok) {
                    throw new Error(`error fetching image: [${r.statusCode}]: ${r.status}`)
                }
                return r.blob()
                //return r.arrayBuffer()
            }
        }
    }
</script>

<style scoped>
    h1 {
        font-weight: 500;
        font-size: 2.6rem;
        position: relative;
        top: -10px;
    }

    h3 {
        font-size: 1.2rem;
    }

    .all {
        display: flex;
        flex-direction: row;
        text-align: center;
    }

    .title {
        text-align: center;
    }

    @media (min-width: 1024px) {
        .greetings h1,
        .greetings h3 {
            text-align: left;
        }
    }
</style>
