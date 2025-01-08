<script setup lang="ts">
import { ref, watch, computed } from 'vue';
import axios from 'axios';

const errorMessage = ref('');
const username = ref('');
const usernameWasEdited = ref(false);
const password = ref('');
const passwordWasEdited = ref(false);
const passwordVisible = ref(false);

watch(username, (newValue) => {
    if (newValue !== '') {
        usernameWasEdited.value = true;
    }
});

watch(password, (newValue) => {
    if (newValue !== '') {
        passwordWasEdited.value = true;
    }
});

const togglePasswordVisibility = () => {
    passwordVisible.value = !passwordVisible.value;
};

const isFormValid = computed(() => {
    return username.value !== '' && password.value !== '';
});

const login = async () => {
    const requestData = {
        username: username.value,
        password: password.value,
    };

    try {
        const response = await axios.post('http://localhost:8080/api/auth', requestData);
        localStorage.setItem('jwtToken', response.data);
    } catch (error) {
        if (axios.isAxiosError(error)) {
            if (error.response && error.response.data) {
                errorMessage.value = error.response.data;
            } else {
                errorMessage.value = '發生未知錯誤，請稍後再試。';
            }
        } else {
            errorMessage.value = '發生未知錯誤，請稍後再試。';
        }
    }
}
</script>

<template>
    <div class="container">
        <nav class="navbar">
            <div class="navbar-container">
                <div class="navbar-content">
                    <a class="navbar-logo" href="/">DShop</a>
                    <div class="login-text">登入</div>
                </div>
                <a href="/help" target="_blank" rel="noopener noreferrer" class="help-link">需要幫助？</a>
            </div>
        </nav>
        <div style="background-color: #ee4d2d;">
            <div class="login-container">
                <div class="login-form">
                    <div class="form-title">
                        <div class="title-text">登入</div>
                    </div>
                    <div class="form-inputs">
                        <div v-if="errorMessage" class="error-message-container">
                            <div class="error-message-icon">
                                <svg viewBox="0 0 16 16" class="error-icon">
                                    <path fill="none" stroke="#FF424F" d="M8 15A7 7 0 108 1a7 7 0 000 14z"
                                        clip-rule="evenodd"></path>
                                    <rect stroke="none" width="7" height="1.5" x="6.061" y="5" fill="#FF424F" rx=".75"
                                        transform="rotate(45 6.06 5)"></rect>
                                    <rect stroke="none" width="7" height="1.5" fill="#FF424F" rx=".75"
                                        transform="scale(-1 1) rotate(45 -11.01 -9.51)"></rect>
                                </svg>
                            </div>
                            <div class="error-message-text">{{ errorMessage }}</div>
                        </div>
                        <form @submit.prevent="login">
                            <div style="margin-bottom: 10px;">
                                <div class="input-group"
                                    :class="{ 'input-group-error': username === '' && usernameWasEdited }">
                                    <input v-model.trim="username" class="input-text" type="text"
                                        placeholder="電話號碼/使用者名稱/Email" autocomplete="on" name="account" maxlength="30">
                                </div>
                                <div class="input-error-message">
                                    <p v-if="username === '' && usernameWasEdited">
                                        請填寫此欄位
                                    </p>
                                </div>
                            </div>
                            <div style="margin-bottom: 14px;">
                                <div class="input-group"
                                    :class="{ 'input-group-error': password === '' && passwordWasEdited }">
                                    <input v-model.trim="password" class="input-text"
                                        :type="passwordVisible ? 'text' : 'password'" placeholder="密碼"
                                        autocomplete="current-password" name="password" maxlength="16">
                                    <button class="eye-icon-button" @click.prevent="togglePasswordVisibility">
                                        <svg v-if="passwordVisible" fill="none" viewBox="0 0 20 12"
                                            class="eye-icon-open">
                                            <path stroke="none" fill="#000" fill-opacity=".54" fill-rule="evenodd"
                                                d="M19.975 5.823V5.81 5.8l-.002-.008v-.011a.078.078 0 01-.002-.011v-.002a.791.791 0 00-.208-.43 13.829 13.829 0 00-1.595-1.64c-1.013-.918-2.123-1.736-3.312-2.368-.89-.474-1.832-.867-2.811-1.093l-.057-.014a2.405 2.405 0 01-.086-.02L11.884.2l-.018-.003A9.049 9.049 0 0010.089 0H9.89a9.094 9.094 0 00-1.78.197L8.094.2l-.016.003-.021.005a1.844 1.844 0 01-.075.017l-.054.012c-.976.226-1.92.619-2.806 1.09-1.189.635-2.3 1.45-3.31 2.371a13.828 13.828 0 00-1.595 1.64.792.792 0 00-.208.43v.002c-.002.007-.002.015-.002.022l-.002.01V5.824l-.002.014a.109.109 0 000 .013L0 5.871a.206.206 0 00.001.055c0 .01 0 .018.002.027 0 .005 0 .009.003.013l.001.011v.007l.002.01.001.013v.002a.8.8 0 00.208.429c.054.067.11.132.165.197a13.9 13.9 0 001.31 1.331c1.043.966 2.194 1.822 3.428 2.48.974.52 2.013.942 3.09 1.154a.947.947 0 01.08.016h.003a8.864 8.864 0 001.596.16h.2a8.836 8.836 0 001.585-.158l.006-.001a.015.015 0 01.005-.001h.005l.076-.016c1.079-.212 2.118-.632 3.095-1.153 1.235-.66 2.386-1.515 3.43-2.48a14.133 14.133 0 001.474-1.531.792.792 0 00.208-.43v-.002c.003-.006.003-.015.003-.022v-.01l.002-.008c0-.004 0-.009.002-.013l.001-.012.001-.015.001-.019.002-.019a.07.07 0 01-.01-.036c0-.009 0-.018-.002-.027zm-6.362.888a3.823 3.823 0 01-1.436 2.12l-.01-.006a3.683 3.683 0 01-2.178.721 3.67 3.67 0 01-2.177-.721l-.009.006a3.823 3.823 0 01-1.437-2.12l.014-.01a3.881 3.881 0 01-.127-.974c0-2.105 1.673-3.814 3.738-3.816 2.065.002 3.739 1.711 3.739 3.816 0 .338-.047.662-.128.975l.011.009zM8.145 5.678a1.84 1.84 0 113.679 0 1.84 1.84 0 01-3.679 0z"
                                                clip-rule="evenodd">
                                            </path>
                                        </svg>

                                        <svg v-else fill="none" viewBox="0 0 20 10" class="eye-icon-closed">
                                            <path stroke="none" fill="#000" fill-opacity=".54"
                                                d="M19.834 1.15a.768.768 0 00-.142-1c-.322-.25-.75-.178-1 .143-.035.036-3.997 4.712-8.709 4.712-4.569 0-8.71-4.712-8.745-4.748a.724.724 0 00-1-.071.724.724 0 00-.07 1c.07.106.927 1.07 2.283 2.141L.631 5.219a.69.69 0 00.036 1c.071.142.25.213.428.213a.705.705 0 00.5-.214l1.963-2.034A13.91 13.91 0 006.806 5.86l-.75 2.535a.714.714 0 00.5.892h.214a.688.688 0 00.679-.535l.75-2.535a9.758 9.758 0 001.784.179c.607 0 1.213-.072 1.785-.179l.75 2.499c.07.321.392.535.677.535.072 0 .143 0 .179-.035a.714.714 0 00.5-.893l-.75-2.498a13.914 13.914 0 003.248-1.678L18.3 6.147a.705.705 0 00.5.214.705.705 0 00.499-.214.723.723 0 00.036-1l-1.82-1.891c1.463-1.071 2.32-2.106 2.32-2.106z">
                                            </path>
                                        </svg>
                                    </button>
                                </div>
                                <div class="input-error-message">
                                    <p v-if="password === '' && passwordWasEdited">
                                        請填寫此欄位
                                    </p>
                                </div>
                            </div>
                            <button class="login-button" :disabled="!isFormValid" type="submit">登入</button>
                        </form>
                        <div class="forgot-password-container">
                            <a class="forgot-password" href="/buyer/reset">忘記密碼</a>
                        </div>
                        <div>
                            <div class="social-divider">
                                <div class="divider-line"></div>
                                <span class="or-text">或</span>
                                <div class="divider-line"></div>
                            </div>
                            <div class="social-buttons">
                                <button class="facebook-button">
                                    <div class="facebook-icon-container">
                                        <div class="facebook-icon"></div>
                                    </div>
                                    <div>Facebook</div>
                                </button>
                                <button class="google-button">
                                    <div class="google-icon-container">
                                        <div class="google-icon"></div>
                                    </div>
                                    <div>Google</div>
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="signup-prompt">
                        <div class="signup">
                            DesignerShop新朋友？
                            <a class="signup-link" href="/buyer/signup">註冊</a>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <footer style="background-color: #f5f5f5;">
            <div class="footer-container">
                <div class="footer-section1">
                    <div class="footer-column">
                        <div class="column-title">客服中心</div>
                        <ul class="column-links">
                            <li class="link-item">
                                <a href="https://help.shopee.tw/tw/s" class="link" title="" target="_blank"
                                    rel="noopener noreferrer">
                                    <span class="link-text">幫助中心</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://help.shopee.tw/portal/article/83489-[%E6%96%B0%E6%89%8B%E4%B8%8A%E8%B7%AF]-%E4%BB%80%E9%BA%BC%E6%98%AF%E8%9D%A6%E7%9A%AE%E5%95%86%E5%9F%8E?"
                                    class="link" title="" target="_blank" rel="noopener noreferrer">
                                    <span class="link-text">蝦皮商城</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://help.shopee.tw/portal/article/80089-[%E6%96%B0%E6%89%8B%E4%B8%8A%E8%B7%AF]-%E8%9D%A6%E7%9A%AE%E8%B3%BC%E7%89%A9%E6%94%AF%E6%8F%B4%E5%93%AA%E4%BA%9B%E4%BB%98%E6%AC%BE%E6%96%B9%E5%BC%8F%E8%88%87%E4%BB%98%E6%AC%BE%E9%87%91%E9%A1%8D%E4%B8%8A%E9%99%90?"
                                    class="link" title="" target="_blank" rel="noopener noreferrer">
                                    <span class="link-text">付款方式</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://help.shopee.tw/portal/article/79770-[%E8%9D%A6%E7%9A%AE%E9%8C%A2%E5%8C%85]-%E4%BB%80%E9%BA%BC%E6%98%AF%E8%9D%A6%E7%9A%AE%E9%8C%A2%E5%8C%85"
                                    class="link" title="" target="_blank" rel="noopener noreferrer">
                                    <span class="link-text">蝦皮錢包</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://help.shopee.tw/portal/article/79806-[%3Cem%3E%E8%9D%A6%3C%2Fem%3E%3Cem%3E%E5%B9%A3%3C%2Fem%3E%E7%9B%B8%E9%97%9C]-%E4%BB%80%E9%BA%BC%E6%98%AF%3Cem%3E%E8%9D%A6%3C%2Fem%3E%3Cem%3E%E5%B9%A3%3C%2Fem%3E"
                                    class="link" title="" target="_blank" rel="noopener noreferrer">
                                    <span class="link-text">蝦幣</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://help.shopee.tw/portal/article/79787-[%e5%85%8d%e9%81%8b]-%e5%85%8d%e9%81%8b%e9%82%84%e6%9c%89%e5%97%8e%ef%bc%9f"
                                    class="link" title="" target="_blank" rel="noopener noreferrer">
                                    <span class="link-text">運費補助</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://help.shopee.tw/portal/article/79724-[%E9%80%80%E8%B2%A8%2F%E9%80%80%E6%AC%BE]-%E9%80%80%E8%B2%A8%E9%80%80%E6%AC%BE%E6%87%B6%E4%BA%BA%E5%8C%85"
                                    class="link" title="" target="_blank" rel="noopener noreferrer">
                                    <span class="link-text">退貨退款</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://help.shopee.tw/portal/article/80020-[%E8%A8%82%E5%96%AE%E4%BF%9D%E9%9A%9C]-%E4%BB%80%E9%BA%BC%E6%98%AF%E5%BB%B6%E9%95%B7%E8%A8%82%E5%96%AE%E6%92%A5%E6%AC%BE"
                                    class="link" title="" target="_blank" rel="noopener noreferrer">
                                    <span class="link-text">延長訂單撥款</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://help.shopee.tw/portal/article/79725-[%e8%81%af%e7%b5%a1%e5%ae%a2%e6%9c%8d]-%e5%a6%82%e4%bd%95%e8%81%af%e7%b5%a1%e8%9d%a6%e7%9a%ae%e5%ae%a2%e6%9c%8d%3F"
                                    class="link" title="" target="_blank" rel="noopener noreferrer">
                                    <span class="link-text">聯絡客服</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://shopee.tw/m/Anti-fraud-advocacy" class="link" title="" target="_blank"
                                    rel="noopener noreferrer">
                                    <span class="link-text">防詐騙宣傳</span>
                                </a>
                            </li>
                        </ul>
                    </div>
                    <div class="footer-column">
                        <div class="column-title">關於蝦皮</div>
                        <ul class="column-links">
                            <li class="link-item">
                                <a href="https://careers.shopee.com/about/" class="link" title="" target="_blank"
                                    rel="noopener noreferrer">
                                    <span class="link-text">關於蝦皮</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://careers.shopee.com/jobs" class="link" title="" target="_blank"
                                    rel="noopener noreferrer">
                                    <span class="link-text">加入我們</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://help.shopee.tw/portal/article/77266" class="link" title=""
                                    target="_blank" rel="noopener noreferrer">
                                    <span class="link-text">蝦皮條款</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://help.shopee.tw/portal/article/77271" class="link" title=""
                                    target="_blank" rel="noopener noreferrer">
                                    <span class="link-text">隱私權政策</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://shopee.tw/mall/" class="link" title="" target="_blank"
                                    rel="noopener noreferrer">
                                    <span class="link-text">蝦皮商城</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://seller.shopee.tw/" class="link" title="" target="_blank"
                                    rel="noopener noreferrer">
                                    <span class="link-text">賣家中心</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://shopee.tw/flash_sale/" class="link" title="" target="_blank"
                                    rel="noopener noreferrer">
                                    <span class="link-text">限時特賣</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="mailto:media.tw%40shopee.com" class="link" title="" target="_blank"
                                    rel="noopener noreferrer">
                                    <span class="link-text">聯絡媒體</span>
                                </a>
                            </li>
                        </ul>
                    </div>
                    <div class="footer-column">
                        <div class="column-title">付款</div>
                        <ul class="column-logos">
                            <li class="logo-item">
                                <a target="_blank" rel="noopener noreferrer" class="logo-link">
                                    <img src="https://down-tw.img.susercontent.com/file/08cad757b16c285bbd768870cb08cf53"
                                        alt="logo">
                                </a>
                            </li>
                            <li class="logo-item">
                                <a target="_blank" rel="noopener noreferrer" class="logo-link">
                                    <img src="https://down-tw.img.susercontent.com/file/c89f5e400512b891bd3dbd9ee25c120e"
                                        alt="logo">
                                </a>
                            </li>
                            <li class="logo-item">
                                <a target="_blank" rel="noopener noreferrer" class="logo-link">
                                    <img src="https://down-tw.img.susercontent.com/file/c59294a3e42f921b8c97a1833234fa0e"
                                        alt="logo">
                                </a>
                            </li>
                        </ul>
                        <div class="column-title" style="margin-top: 0;">物流合作</div>
                        <ul class="column-logos">
                            <li class="logo-item">
                                <a target="_blank" rel="noopener noreferrer" class="logo-link">
                                    <img src="https://down-tw.img.susercontent.com/file/fcce0621aec0a5409c1e7d5252cfd136"
                                        alt="logo">
                                </a>
                            </li>
                            <li class="logo-item">
                                <a target="_blank" rel="noopener noreferrer" class="logo-link">
                                    <img src="https://down-tw.img.susercontent.com/file/f316a8e81ba98aa684d0f278be9c4c05"
                                        alt="logo">
                                </a>
                            </li>
                            <li class="logo-item">
                                <a target="_blank" rel="noopener noreferrer" class="logo-link">
                                    <img src="https://down-tw.img.susercontent.com/file/86bf82557d870ef093cc511ae243a59c"
                                        alt="logo">
                                </a>
                            </li>
                            <li class="logo-item">
                                <a target="_blank" rel="noopener noreferrer" class="logo-link">
                                    <img src="https://down-tw.img.susercontent.com/file/tw-50009109-97a9b580c9f04ec12d649b23bd2e4702"
                                        alt="logo">
                                </a>
                            </li>
                            <li class="logo-item">
                                <a target="_blank" rel="noopener noreferrer" class="logo-link">
                                    <img src="https://down-tw.img.susercontent.com/file/872bb8711db001a33e0e03e21860cbb0"
                                        alt="logo">
                                </a>
                            </li>
                            <li class="logo-item">
                                <a target="_blank" rel="noopener noreferrer" class="logo-link">
                                    <img src="https://down-tw.img.susercontent.com/file/tw-50009109-3bdda36a50e955d9bcf1c46748ee7657"
                                        alt="logo">
                                </a>
                            </li>
                            <li class="logo-item">
                                <a target="_blank" rel="noopener noreferrer" class="logo-link">
                                    <img src="https://down-tw.img.susercontent.com/file/tw-50009109-10af8e8aad4af4dbb239d46682a47435"
                                        alt="logo">
                                </a>
                            </li>
                        </ul>
                        <div class="column-title" style="margin-top: 0;">蝦皮直送包裝減量標章</div>
                        <ul class="column-logos">
                            <li class="logo-item">
                                <a target="_blank" rel="noopener noreferrer" class="logo-link">
                                    <img src="https://down-tw.img.susercontent.com/file/f53618ce2c31cba7a71e4ea4fa08e93c"
                                        alt="logo">
                                </a>
                            </li>
                        </ul>
                    </div>
                    <div class="footer-column">
                        <div class="column-title">關注我們</div>
                        <ul class="column-links">
                            <li class="link-item">
                                <a href="https://facebook.com/ShopeeTW" class="link" title="" target="_blank"
                                    rel="noopener noreferrer">
                                    <img class="social-media-icon"
                                        src="https://down-tw.img.susercontent.com/file/952b3cd5f11daa5242f9448fb76b5ae2">
                                    <span class="link-text">Facebook</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://instagram.com/Shopee_TW" class="link" title="" target="_blank"
                                    rel="noopener noreferrer">
                                    <img class="social-media-icon"
                                        src="https://down-tw.img.susercontent.com/file/fc9052e647a0ec204e480fc27a35309f">
                                    <span class="link-text">Instagram</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://page.line.me/shopee?openQrModal=true" class="link" title=""
                                    target="_blank" rel="noopener noreferrer">
                                    <img class="social-media-icon"
                                        src="https://down-tw.img.susercontent.com/file/8e2a1c69fe7255b705d0687c779fa962">
                                    <span class="link-text">Line</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://linkedin.com/company/shopee" class="link" title="" target="_blank"
                                    rel="noopener noreferrer">
                                    <img class="social-media-icon"
                                        src="https://down-tw.img.susercontent.com/file/144b82f1a4b78ebdc1ea68ec15a9eeae">
                                    <span class="link-text">LinkedIn</span>
                                </a>
                            </li>
                            <li class="link-item">
                                <a href="https://shopee.tw/blog" class="link" title="" target="_blank"
                                    rel="noopener noreferrer">
                                    <img class="social-media-icon"
                                        src="https://down-tw.img.susercontent.com/file/b3153fde9f151c179cb2b4d854205ab9">
                                    <span class="link-text">蝦品輯部落格</span>
                                </a>
                            </li>
                        </ul>
                    </div>
                    <div class="footer-column">
                        <div class="column-title">下載蝦皮</div>
                        <div class="column-downloads">
                            <a href="https://shopee.tw/web" target="_blank" rel="noopener noreferrer">
                                <img src="https://down-tw.img.susercontent.com/file/4b01eb6a8fb3e25d7bca3805e1ebf3a4"
                                    alt="download_qr_code" class="download-qr-code">
                            </a>
                            <div class="download-links">
                                <a href="https://shopee.tw/web" target="_blank" rel="noopener noreferrer"
                                    class="download-link">
                                    <img src="https://down-tw.img.susercontent.com/file/4e4f8912bf8ff66be5c95fb2fe945358"
                                        alt="app">
                                </a>
                                <a href="https://shopee.tw/web" target="_blank" rel="noopener noreferrer"
                                    class="download-link">
                                    <img src="https://down-tw.img.susercontent.com/file/dc9067c7bbb920656633cdca0cf40e6c"
                                        alt="app">
                                </a>
                                <a href="https://shopee.tw/web" target="_blank" rel="noopener noreferrer"
                                    class="download-link">
                                    <img src="https://down-tw.img.susercontent.com/file/a28a7b61236921ec5b618815dafed1f8"
                                        alt="app">
                                </a>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="footer-section2">
                    <div class="company-info">新加坡商蝦皮娛樂電商有限公司台灣分公司　　統一編號：56801904</div>
                    <div class="copyright">© 2024 Shopee. 版權所有。</div>
                    <div class="country-links">
                        <div class="country-link">
                            <a href="https://shopee.sg" class="country-link-text">新加坡</a>
                        </div>
                        <div class="country-link">
                            <a href="https://shopee.co.id" class="country-link-text">印尼</a>
                        </div>
                        <div class="country-link">
                            <a href="https://shopee.co.th" class="country-link-text">泰國</a>
                        </div>
                        <div class="country-link">
                            <a href="https://shopee.com.my" class="country-link-text">馬來西亞</a>
                        </div>
                        <div class="country-link">
                            <a href="https://shopee.vn" class="country-link-text">越南</a>
                        </div>
                        <div class="country-link">
                            <a href="https://shopee.ph" class="country-link-text">菲律賓</a>
                        </div>
                        <div class="country-link">
                            <a href="https://shopee.com.br" class="country-link-text">巴西</a>
                        </div>
                        <div class="country-link">
                            <a href="https://shopee.com.mx" class="country-link-text">墨西哥</a>
                        </div>
                        <div class="country-link">
                            <a href="https://shopee.com.co" class="country-link-text">哥倫比亞</a>
                        </div>
                        <div class="country-link">
                            <a href="https://shopee.cl" class="country-link-text">智利</a>
                        </div>
                        <div class="country-link">
                            <a href="https://shopee.tw" class="country-link-text">台灣</a>
                        </div>
                    </div>
                </div>
            </div>
        </footer>
    </div>
</template>

<style scoped>
.container {
    background-color: #ffffff;
    min-width: 1260px;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    position: relative;
    flex: 1;
}

.navbar {
    height: 84px;
    box-shadow: 0 6px 6px rgba(0, 0, 0, .06);
    display: flex;
    align-items: center;
}

.navbar-container {
    margin: 0 auto;
    width: 1200px;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.navbar-content {
    display: flex;
    align-items: center;
}

.navbar-logo {
    margin-right: 10px;
    font-family: 'Plaster', sans-serif;
    font-size: 40px;
    text-decoration: none;
    color: #ff5733;
}

.login-text {
    font-size: 24px;
    color: #222222;
}

.help-link {
    margin-right: 15px;
    cursor: pointer;
    font-size: 14px;
    text-decoration: none;
    color: #ee4d2d;
}

.login-container {
    margin: 0 auto;
    width: 1040px;
    height: 600px;
    min-height: 600px;
    background-image: url("https://down-tw.img.susercontent.com/file/sg-11134004-7rfi9-m3zupmrddxf4d7");
    background-position: center center;
    background-size: contain;
    background-repeat: no-repeat;
    display: flex;
    justify-content: flex-end;
    align-items: center;
}

.login-form {
    border-radius: 4px;
    background-color: #ffffff;
    width: 400px;
    box-sizing: border-box;
    box-shadow: 0 3px 10px 0 rgba(0, 0, 0, .14);
    overflow: hidden;
}

.form-title {
    min-height: 80px;
    box-sizing: border-box;
    display: flex;
    justify-content: flex-start;
    align-items: center;
}

.title-text {
    margin: 22px 30px;
    max-width: 136px;
    font-size: 20px;
    color: #222222;
    flex-shrink: 0;
}

.form-inputs {
    padding: 0 30px 30px;
    overflow: hidden;
}

.error-message-container {
    margin-bottom: 25px;
    border: 1px solid rgba(255, 66, 79, .2);
    border-radius: 2px;
    padding: 12px 15px;
    background-color: #fff9fa;
    height: 40px;
    box-sizing: border-box;
    display: flex;
    justify-content: flex-start;
    align-items: center;
}

.error-message-icon {
    margin-right: 10px;
    width: 16px;
    display: flex;
}

.error-icon {
    width: 16px;
    height: 16px;
    color: #ff424f;
    overflow: hidden;
}

.error-message-text {
    font-size: 14px;
    color: #222222;
}

.input-group {
    border: 1px solid rgba(0, 0, 0, .14);
    border-radius: 2px;
    width: 100%;
    height: 40px;
    box-sizing: border-box;
    box-shadow: inset 0 2px 0 rgba(0, 0, 0, .02);
    display: flex;
    align-items: center;
    overflow: hidden;
}

.input-group-error {
    border-color: #ff424f;
}

.input-text {
    margin: 0;
    border: 0;
    padding: 12px;
    height: 16px;
    color: rgba(0, 0, 0, .8);
    line-height: normal;
    flex: 1;
    flex-shrink: 0;
    filter: none;
    outline: none;
}

.input-text::placeholder {
    color: rgba(0, 0, 0, .14);
}

.input-error-message {
    margin: 0;
    padding: 4px 0 0;
    height: 24px;
    font-size: 12px;
    color: #ff424f;
}

.eye-icon-button {
    margin: 0;
    border: 0;
    padding: 0 15px 0 12px;
    background: transparent;
    cursor: pointer;
    outline: none;
    text-transform: none;
    display: flex;
    align-items: center;
    overflow: visible;
}

.eye-icon-open {
    width: 20px;
    height: 12px;
}

.eye-icon-closed {
    padding-top: 6px;
    width: 20px;
    height: 16px;
}

.login-button:disabled {
    opacity: .7;
    cursor: not-allowed;
}

.login-button {
    margin: 0;
    border: 0;
    border-radius: 2px;
    padding: 0 10px;
    background-color: #ee4d2d;
    width: 100%;
    min-width: 140px;
    height: 40px;
    box-shadow: 0 1px 1px rgba(0, 0, 0, .09);
    color: #ffffff;
    outline: none;
    overflow: visible;
}

.forgot-password-container {
    margin: 10px 0;
    display: flex;
    justify-content: flex-start;
}

.forgot-password {
    font-size: 12px;
    text-decoration: none;
    color: #0055aa;
}

.social-divider {
    padding-bottom: 14px;
    display: flex;
    align-items: center;
}

.divider-line {
    background-color: #dbdbdb;
    width: 100%;
    height: 1px;
    flex: 1;
}

.or-text {
    padding: 0 16px;
    font-size: 12px;
    color: #ccc;
}

.social-buttons {
    margin: 0 -5px;
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
}

.facebook-button,
.google-button {
    margin: 5px;
    border: 1px solid rgba(0, 0, 0, .26);
    border-radius: 2px;
    padding: 0 2px;
    padding-right: 8px;
    background-color: #ffffff;
    width: 100%;
    height: 40px;
    box-sizing: border-box;
    cursor: pointer;
    outline: none;
    text-transform: none;
    font-size: 14px;
    color: rgba(0, 0, 0, .87);
    display: flex;
    justify-content: center;
    align-items: center;
    flex: 1;
    overflow: visible;
}

.facebook-icon-container,
.google-icon-container {
    border-radius: 1px;
    width: 36px;
    height: 36px;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-shrink: 0;
}

.facebook-icon {
    width: 22px;
    height: 22px;
    background-image: url("https://deo.shopeemobile.com/shopee/shopee-pcmall-live-sg/assets/7ffa33c1e9518f30.png");
    background-position: 5.555555555555555% 62.666666666666664%;
    background-size: 325% 287.5%;
    flex-shrink: 0;
}

.google-icon {
    width: 22px;
    height: 22px;
    background-image: url("https://deo.shopeemobile.com/shopee/shopee-pcmall-live-sg/assets/7ffa33c1e9518f30.png");
    background-position: 83.92857142857143% 5.154639175257732%;
    background-size: 722.2222222222222% 638.8888888888889%;
    flex-shrink: 0;
}

.signup-prompt {
    margin-bottom: 30px;
}

.signup {
    padding-right: 4px;
    width: 100%;
    font-size: 14px;
    color: rgba(0, 0, 0, .26);
    white-space: pre;
    display: flex;
    justify-content: center;
    align-items: center;
}

.signup-link {
    cursor: pointer;
    font-size: 14px;
    font-weight: 500;
    text-decoration: none;
    color: #ee4d2d;
}

.footer-container {
    margin: auto;
    width: 1260px;
}

.footer-section1 {
    margin: 0 -5px;
    padding: 5px;
    width: 100%;
    min-width: 1260px;
    display: flex;
    align-items: flex-start;
}

.footer-column {
    padding: 5px;
    width: 250px;
    box-sizing: border-box;
}

.column-title {
    margin-top: 37px;
    margin-bottom: 16px;
    font-size: 12px;
    font-weight: 700;
    color: rgba(0, 0, 0, .87);
}

.column-links {
    margin: 0 0 16px;
    padding: 0;
    list-style-type: none;
    text-decoration: none;
    color: rgba(0, 0, 0, .65);
}

.link-item {
    margin-bottom: 7px;
    font-size: 12px;
}

.link {
    text-decoration: none;
    color: rgba(0, 0, 0, .65);
    display: flex;
    align-items: center;
    overflow: hidden;
}

.link-text {
    max-width: 100%;
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow: hidden;
}

.column-logos {
    margin: 0 0 16px;
    padding: 0;
    list-style-type: none;
    display: flex;
    flex-wrap: wrap;
}

.logo-item {
    margin-right: 8px;
    margin-bottom: 8px;
    border-radius: 2px;
    padding: 4px;
    background-color: #ffffff;
    width: 60;
    height: 30px;
    box-sizing: border-box;
    box-shadow: 0 1px 1px rgba(0, 0, 0, .2);
    display: flex;
    justify-content: center;
    align-items: center;
    overflow: hidden;
}

.logo-link {
    width: 100%;
    height: 100%;
    box-sizing: border-box;
    text-align: center;
    text-decoration: none;
    display: flex;
    justify-content: center;
    align-items: center;
}

.social-media-icon {
    margin-right: 8px;
    border: 0;
    width: 16px;
    height: 16px;
}

.column-downloads {
    margin-bottom: 16px;
    width: 100%;
    display: flex;
    flex-direction: row;
}

.download-qr-code {
    margin-right: 12px;
    border-radius: 2px;
    padding: 4px;
    background-color: #ffffff;
    width: 88px;
    height: 88px;
    box-shadow: 0 1px 1px rgba(0, 0, 0, .2);
}

.download-links {
    width: 76px;
    vertical-align: top;
    display: inline-block;
}

.download-link {
    margin-bottom: 8px;
    border-radius: 2px;
    padding: 4px;
    background-color: #ffffff;
    width: 76px;
    height: 24px;
    box-shadow: 0 1px 1px rgba(0, 0, 0, .2);
    text-decoration: none;
    display: flex;
    justify-content: center;
    align-items: center;
}

.footer-section2 {
    border-top: 1px solid rgba(0, 0, 0, .1);
    padding: 40px 0;
    font-size: 14px;
    color: rgba(0, 0, 0, .54);
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    flex-wrap: wrap;
}

.company-info {
    margin-bottom: 12px;
    flex-basis: 100%;
}

.copyright {
    margin-right: 25px;
    line-height: 18px;
    flex-shrink: 0;
}

.country-links {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
}

.country-link {
    padding: 0 5px;
}

.country-link:not(:last-child) {
    border-right: 1px solid rgba(0, 0, 0, .2);
}

.country-link-text {
    text-decoration: none;
    color: rgba(0, 0, 0, .54);
    line-height: 18px;
}
</style>
