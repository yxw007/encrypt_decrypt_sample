<template>
    <div class="hello">
        <h3>{{aes_title}}</h3>
        <div style="display: flex;">
            <div class="block">
                <p>①明文加密 =>
                    <button v-on:click="aesEncrypt">加密</button>
                </p>
                <textarea ref="aesPlainText" rows="5" cols="15"></textarea>
            </div>
            <div class="block">
                <p>②密文解密 =>
                    <button v-on:click="aesDecrypt">解密</button>
                </p>
                <textarea ref="aesCiphertext" rows="5" cols="15"></textarea>
            </div>
            <div class="block">
                <p>③解密后的明文</p>
                <textarea ref="aesDecryptedCiphertext" rows="5" cols="15"></textarea>
            </div>
        </div>
        <h3>{{rsa_title}}</h3>
        <div style="display: flex;">
            <div class="block">
                <p>①明文加密 =>
                    <button v-on:click="rsaEncrypt()">加密</button>
                </p>
                <textarea ref="rsaPlaintext" rows="5" cols="15"></textarea>
            </div>
            <div class="block">
                <p>②密文解密 =>
                    <button v-on:click="rsaDecrypt()">解密</button>
                </p>
                <textarea ref="rsaCiphertext" rows="5" cols="15"></textarea>
            </div>
            <div class="block">
                <p>③解密后的明文</p>
                <textarea ref="rsaDecryptedCiphertext" rows="5" cols="15"></textarea>
            </div>
        </div>
        <h3>{{hybird_title}}</h3>
        <div style="display: flex;">
            <div class="block">
                <p>①明文加密 =>
                    <button v-on:click="hybirdEncrypt">加密</button>
                </p>
                <textarea ref="hybirdPlaintext" rows="5" cols="15"></textarea>
            </div>
            <div class="block">
                <p>②密文解密 =>
                    <button v-on:click="hybirdDecrypt">解密</button>
                </p>
                <textarea ref="hybirdCiphertext" rows="5" cols="15"></textarea>
            </div>
            <div class="block">
                <p>③解密后的明文</p>
                <textarea ref="hybirdDecryptedCiphertext" rows="5" cols="15"></textarea>
            </div>
        </div>
    </div>
</template>

<script>
    import {JSEncrypt} from 'jsencrypt';
    import CryptoJS from 'crypto-js';

    export default {
        name: 'AES 对称加密demo',
        props: {
            msg: String
        },
        data() {
            return {
                aes_title: "AES 对称加密demo",
                key: "",
                iv: "",
                rsa_title: "RSA 非对称加密demo",
                rsa_pub_key: "-----BEGIN PUBLIC KEY-----\n" +
                    "MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC2aV1AoO8GUVSPBdasXnmD0tiY\n" +
                    "JutVZ1hB5PeS22We4oMIVW/9mKuvPv1ouZPJrMfpDBgQRhBZ5c1Y/3Oti8jP5ihE\n" +
                    "qeTQ08ShnUmbkUVPxXPM5TQQgxaTHjyELM7IEUipYtb1dqRtKsvJr725Pnszf6nz\n" +
                    "tJvoTBKs7fydMZBkxwIDAQAB\n" +
                    "-----END PUBLIC KEY-----\n",
                rsa_pri_key: "-----BEGIN RSA PRIVATE KEY-----\n" +
                    "MIICXAIBAAKBgQC2aV1AoO8GUVSPBdasXnmD0tiYJutVZ1hB5PeS22We4oMIVW/9\n" +
                    "mKuvPv1ouZPJrMfpDBgQRhBZ5c1Y/3Oti8jP5ihEqeTQ08ShnUmbkUVPxXPM5TQQ\n" +
                    "gxaTHjyELM7IEUipYtb1dqRtKsvJr725Pnszf6nztJvoTBKs7fydMZBkxwIDAQAB\n" +
                    "AoGBAIiR7GKV2z+EpuWJ/ocBGMNsmgOYp/tCK57yObW3E6dYebhEl1tr8aZ8Z6f/\n" +
                    "wTluZiICjwWoH1ffKNZoM4iMrqRMZ8tyvWzzh03NcGGN/XHEE74LTHksEXpwykcd\n" +
                    "eDrgrna0UKk1+C323PmCA0LY23jknzAew3lrXOCbyvyTGVpJAkEA6WIDtSgeYShZ\n" +
                    "S3N6OKgmvKeRQDzNVSaSY9OqjgvTNUZc1NoeWOcRRwSc4mJ5UYeEm/LOiB3nHGCN\n" +
                    "ULWZL9uubQJBAMgWziUyUZkg/qGOgvtX3q8v4kQCUMc8Vde7ilqsWmO97xuUXtT7\n" +
                    "S2XAzCaVugArxAz1S7LyEaiJ5II3k1kMz4MCQGLaKDXgQ1Xl0ES8KeW7m4TG+Sgb\n" +
                    "WOGbT+BGtHQsIA7tub5SkQ4Y+WF6W7Ur/rUA0LN5We+fstd7MgAgmz0BMNUCQBdb\n" +
                    "IiERaJj5Uv/ExOFV9nZ4nm7V3lwDXPnbuGCxMbPm3dxYS2GNG9X61VnDrHyMn0vr\n" +
                    "7jQrMYh84CGbHyYL6sUCQAza6oSLNO2lI2NUbr4CwbTyc98K2Flo97A1jchkcdDb\n" +
                    "Vfp6x30YlCF4sCPqSxKCIMGGTgzgDzldIpsjPiQROfQ=\n" +
                    "-----END RSA PRIVATE KEY-----\n",
                rsaEncryptor: {},
                rsaDecryptor: {},
                hybird_title: "AES与RSA 混合加密Demo"
            };
        },
        methods: {
            //-------------------------------------------------------------------------
            aesEncrypt() {
                let plaintext = this.$refs.aesPlainText.value;
                let encryptData = this.__aesEncrypt(this.iv, this.key, plaintext);
                this.$refs.aesCiphertext.value = encryptData;
            },
            //-------------------------------------------------------------------------
            __aesEncrypt(iv, key, content) {
                let text = CryptoJS.enc.Utf8.parse(JSON.stringify(content));
                let encrypted = CryptoJS.AES.encrypt(text, key,
                    {
                        iv: iv,
                        mode: CryptoJS.mode.CBC,
                        padding: CryptoJS.pad.Pkcs7,
                    });
                return encrypted.toString();
            },
            //-------------------------------------------------------------------------
            aesDecrypt() {
                let ciphertext = this.$refs.aesCiphertext.value;
                let decryptData = this.__aesDecrypt(this.iv, this.key, ciphertext);
                this.$refs.aesDecryptedCiphertext.value = decryptData;
            },
            //-------------------------------------------------------------------------
            __aesDecrypt(iv, key, content) {
                let decrypt = CryptoJS.AES.decrypt(content, key, {
                    iv: iv,
                    mode: CryptoJS.mode.CBC,
                    padding: CryptoJS.pad.Pkcs7,
                });
                let decryptText = decrypt.toString(CryptoJS.enc.Utf8);
                return decryptText.replace(/\"/g, "");
            },
            //-------------------------------------------------------------------------
            rsaEncrypt() {
                let plaintext = this.$refs.rsaPlaintext.value;
                this.$refs.rsaCiphertext.value = this.__rsaEncrypt(plaintext);
            },
            //-------------------------------------------------------------------------
            __rsaEncrypt(content) {
                return this.rsaEncryptor.encrypt(content);
            },
            //-------------------------------------------------------------------------
            rsaDecrypt() {
                let ciphertext = this.$refs.rsaCiphertext.value;
                this.$refs.rsaDecryptedCiphertext.value = this.__rsaDecrypt(ciphertext);
            },
            //-------------------------------------------------------------------------
            __rsaDecrypt(content) {
                return this.rsaDecryptor.decrypt(content);
            },
            //-------------------------------------------------------------------------
            getRandomAESKey() {
                return (
                    Math.random().toString(36).substring(2, 10) +
                    Math.random().toString(36).substring(2, 10)
                );
            },
            //-------------------------------------------------------------------------
            hybirdEncrypt() {
                const iv = this.getRandomAESKey();
                const key = this.getRandomAESKey();
                let plaintext = this.$refs.hybirdPlaintext.value;
                let originInfo = {iv: iv, key: key, data: plaintext};

                let encryptInfo = this.__hybirdEncrypt(iv, key, plaintext);

                console.log("原始数据：" + JSON.stringify(originInfo) + "\n 加密数据：" + JSON.stringify(encryptInfo));
                this.$refs.hybirdCiphertext.value = JSON.stringify(encryptInfo);
            },
            //-------------------------------------------------------------------------
            __hybirdEncrypt(iv, key, content) {
                const aesEncryptData = this.__aesEncrypt(iv, key, content);
                const rsaEncryptIv = this.__rsaEncrypt(iv);
                const rsaEncryptKey = this.__rsaEncrypt(key);
                return {
                    iv: rsaEncryptIv,
                    key: rsaEncryptKey,
                    data: aesEncryptData,
                };
            },
            //-------------------------------------------------------------------------
            hybirdDecrypt() {
                let ciphertext = this.$refs.hybirdCiphertext.value;
                let encryptInfo = JSON.parse(ciphertext);
                let decryptData = this.__hybirdDecrypt(encryptInfo);
                this.$refs.hybirdDecryptedCiphertext.value = decryptData;
            },
            //-------------------------------------------------------------------------
            __hybirdDecrypt(encryptInfo) {
                const iv = this.rsaDecryptor.decrypt(encryptInfo.iv);
                const key = this.rsaDecryptor.decrypt(encryptInfo.key);
                const data = encryptInfo.data;
                return this.__aesDecrypt(iv, key, data);
            }
            //-------------------------------------------------------------------------
        },
        mounted() {
            //-------------------------------------------------------------------------
            this.key = CryptoJS.enc.Utf8.parse("0123456789abcdef");
            this.iv = CryptoJS.enc.Utf8.parse("abcdef0123456789");
            //-------------------------------------------------------------------------
            this.rsaEncryptor = new JSEncrypt();
            this.rsaEncryptor.setPublicKey(this.rsa_pub_key);
            this.rsaDecryptor = new JSEncrypt();
            this.rsaDecryptor.setPrivateKey(this.rsa_pri_key);
            //-------------------------------------------------------------------------
        }
    }

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    h3 {
        margin: 40px 0 0;
        text-align: left;
        vertical-align: middle;
    }

    ul {
        list-style-type: none;
        padding: 0;
    }

    li {
        display: inline-block;
        margin: 0 10px;
    }

    a {
        color: #42b983;
    }
</style>
