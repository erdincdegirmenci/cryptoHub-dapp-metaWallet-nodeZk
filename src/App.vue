<template>
  <v-app>
    <v-main>
      <div class="centered-image">
        <a href="javascript:void(0);" @click="handleImageClick">
          <img src="@/assets/gif/test.gif" alt="Centered Image" style="width: 200px; height: auto;" />
        </a>
      </div>
    </v-main>

    <div class="wallet-info" v-if="walletAddress">
        <div class="eth-info">
          <div class="balance" style="display: flex; align-items: center; justify-content: flex-start;">
            <img src="@\assets\ethereum_logo.png" alt="Ether Icon" style="width: 30px; height: 30px; margin-right: 10px; background-color: white; border-radius: 50%;" />
            <div style="display: flex; flex-direction: column; text-align: right;">
              <p class="value" style="margin: 0;">{{ etherBalance }}</p>
            </div>
          </div>

        <div class="wallet">
          <div class="value">
            {{ formattedWalletAddress }}
          </div>
          <div>
            <button class="show-button" v-if="formattedWalletAddress" @click="disconnectWallet">Disconnect</button>
          </div>
        </div>
      </div>
    </div>

    <!-- Disconnect Overlay -->
    <div class="overlay" v-if="showOverlay">
      <img src="@/assets/gif/overlay.gif" alt="Loading" style="width: 150px; height: 150px;" />
    </div>

    <!-- Twitter Paylaşım Formu -->
    <div class="popup" v-if="showPopup">
      <div class="popup-overlay"></div>
      <div class="popup-content">
        <div class="popup-step">
          <div class="step-number">1</div>
          <div class="step-label">Connect Wallet</div>
          <div class="step-button-container">
            <button class="step-button" @click="connectWallet">Connect Wallet</button>
          </div>
          <div class="right-rectangle">
            <img src="@/assets/dotgreen.png" alt="Your Image" style="width: 20px; height: 20px;" v-if="walletAddress" />
            <img src="@/assets/dotred.png" alt="Your Image" style="width: 20px; height: 20px;" v-else />
          </div>
        </div>
        <div class="popup-step">
          <div class="step-number">2</div>
          <div class="step-label">Connect X</div>
          <div class="step-button-container">
            <button class="step-button" @click="tweetPopup">Connect X & Share</button>
          </div>
          <div class="right-rectangle">
            <img src="@/assets/dotgreen.png" alt="Your Image" style="width: 20px; height: 20px;" v-if="showDotGreenTwittter" />
            <img src="@/assets/dotred.png" alt="Your Image" style="width: 20px; height: 20px;" v-if="showDotRedTwitter" />
          </div>
        </div>
        <!-- <div class="popup-step">
          <div class="step-number">3</div>
          <div class="step-label">Hold NFT</div>
          <div class="step-button-container">
            <button class="step-button" @click="claim">Hold</button>
          </div>
          <div class="right-rectangle">
            <img src="src/assets/dotgreen.png" alt="Your Image" style="width: 20px; height: 20px;" v-if="showDotGreen" />
            <img src="src/assets/dotred.png" alt="Your Image" style="width: 20px; height: 20px;" v-else />
          </div>
        </div> -->
      </div>
    </div>
  </v-app>
</template>

<script>
import Web3 from 'web3';

export default {
  data() {
    return {
      walletAddress: null,
      etherBalance: null,
      showDisconnectButton: false,
      showOverlay: false,
      tweetText: "test",
      showPopup: false,
      showDotGreen: false,
      showDotRed: true,
      showDotGreenTwittter: false,
      showDotRedTwitter: true,
    };
  },
  computed: {
    formattedWalletAddress() {
      if (!this.walletAddress) {
        return '';
      }

      const addressStart = this.walletAddress.slice(0, 4);
      const addressEnd = this.walletAddress.slice(-4);
      return `${addressStart}...${addressEnd}`;
    },
  },
  methods: {
    async handleImageClick() {
      this.showOverlay = true;
      setTimeout(() => {
        this.showPopup = true;
        this.showOverlay = false;
      }, 1500);
    },
    async connectWallet() {
      this.showOverlay = true;
      setTimeout(async () => {
        if (window.ethereum) {
          try {
            const web3 = new Web3(window.ethereum);
            await ethereum.enable();
            // const accounts = await ethereum.request({ method: 'eth_accounts' });
              const accounts = await web3.eth.getAccounts();
            if (accounts.length > 0) {
              this.walletAddress = accounts[0];
              // const balanceInWei = await ethereum.request({
              //   method: 'eth_getBalance',
              //   params: [this.walletAddress, 'latest'],
              // });
              const balanceInWei= await web3.eth.getBalance(this.walletAddress);
              // this.etherBalance = web3.utils.fromWei(balanceInWei, 'ether');
              // this.etherBalance = parseFloat(this.etherBalance).toFixed(5);
              this.etherBalance = web3.utils.fromWei(balanceInWei, 'ether');
              this.etherBalance = parseFloat(this.etherBalance).toFixed(5);
            }
            ethereum.autoRefreshOnNetworkChange = false;
            ethereum.selectedAddress = null;
          } catch (error) {
            console.error('Cüzdan açma hatası:', error);
          }
        } else {
          console.log('Ethereum cüzdanı bulunamadı. Lütfen MetaMask veya uyumlu bir Ethereum cüzdanı yükleyin ve etkinleştirin.');
        }
        setTimeout(() => {
          this.showOverlay = false;
        }, 1500);
      }, 0);
    },
    async tweetPopup() {
      try {
        const tweetText = "Unlock the Tweet-to-Earn era with #nodeZK!\n\nYour tweets can now earn you $?" +
        " Join me in @nodeZK.\n\nYour journey begins with your very first commitment!";
        
        const twitterUrl = `https://twitter.com/intent/tweet?text=${encodeURIComponent(tweetText)}`;

        const success = window.open(twitterUrl, '_blank');

        if (success) {
          this.showDotGreenTwittter = true;
          this.showDotRedTwitter = false;
        } else {
          this.showDotGreenTwittter = false;
          this.showDotRedTwitter = true;
        }
      } catch (error) {
        console.error('Tweet atma hatası:', error);
        this.showDotGreenTwittter = false;
        this.showDotRedTwitter = true;
      }
    },
    async disconnectWallet() {
      debugger
      if (window.ethereum) {
        try {
          this.etherBalance = null;
          this.walletAddress = null;
          this.showOverlay = true;
          await new Promise(resolve => setTimeout(resolve, 2000));
          this.showOverlay = false;
        } catch (error) {
          console.error('Cüzdanı devre dışı bırakma hatası:', error);
          this.showOverlay = false;
        }
      } else {
        console.log('Ethereum cüzdanı bulunamadı.');
      }
    },
  },
};
</script>


<style scoped>
.popup-step {
  display: flex;
  align-items: center;
}

.right-rectangle {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 30px;
  height: 30px;
  background-color: transparent; /* Arka planı şeffaf yapın */
  margin-left: 10px;
}


.icon {
  font-size: 18px; /* İkonun boyutunu ayarlayın */
}
.popup-step {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 10px; /* Adımlar arasındaki boşluğu ayarlayabilirsiniz */
}

.step-button-container {
  margin-left: 10px; /* Kutu ile düğme arasındaki boşluğu ayarlayabilirsiniz */
}


.popup {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 999;
  display: flex;
  align-items: center;
  justify-content: center;
}

.popup-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.7); /* Saydamlık ayarı */
  z-index: -1;
}

.popup-content {
  background-color: #222; /* Dark Mod arka plan rengi */
  color: #fff; /* Yazı rengi - Beyaz */
  border: 2px solid #555; /* Keskin kenarlar için sınırlar */
  border-radius: 10px; /* Köşeleri yuvarlatmak için */
  padding: 20px; /* İçerik boşluğu eklemek için */
  display: flex;
  flex-direction: column;
  box-shadow: -6px 6px #333;
  border-radius: 5px;
}


.popup-step {
  margin-bottom: 20px; /* Sabit boşluk ekler */
  display: flex;
  align-items: center;
}

.step-number {
  box-shadow: -3px 3px #333;
  background-color: #fff;
  color: #333;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 10px;
}

.step-label {
  font-size: 16px;
  margin: 0;
  color: #fff; /* Label rengini beyaz yapar */
  margin-right: 20px; /* Label ile düğme arasında boşluk ekler */
}

.step-button-container {
  margin-left: auto; /* Butonları sağa yaslar */
}

.step-button {
  background-color: #333;
  color: #fff;
  border: 2px solid #555;
  border-radius: 5px;
  padding: 10px 20px;
  cursor: pointer;
  width: 150px; /* Düğme boyutunu sabitler */
}

.step-button:hover {
  background-color: #444;
}

/* Gölge efekti */
.popup-content {
}
.show-button {
  border: 5px solid white; /* Beyaz sınır eklemek için */
  display: none;
  background-color: #333; /* Arka plan rengi siyah */
  color: white; /* Yazı rengi beyaz */
  padding: 10px 20px; /* Daha büyük boyut */
  width: 150px; /* Genişlik ayarı */
  border-radius: 5px;
  cursor: pointer;
  position: absolute;
  left: 50%; /* Butonu yatayda ortalamak için */
  transform: translateX(-50%); /* Butonu yatayda ortalamak için */
  top: 170%; /* Butonu aşağı kaydırmak için top değerini artırın */
  box-shadow: 0 4px dimgray, inset 4px 0 dimgray;
}

.wallet:hover .show-button {
  display: block;
}


  body, p, div, span, a {
    font-family: 'Courier New', sans-serif;
    font-weight: bold;
  }
.eth-info {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.balance, .wallet {
  background-color: #333;
  padding: 10px;
  border-radius: 5px;
}

.title {
  font-size: 16px;
  color: white;
  margin: 0;
}

.value {
  font-size: 20px;
  color: white;
  margin: 0;
}

.disconnect-button {
  background-color: #ff5733;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
}

.disconnect-button:hover {
  background-color: #e03e1f;
}
.overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.8); /* Arkaplanı daha fazla karart */
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999; /* Overlay üstte olmalı */
}

.overlay img {
  width: 100px; /* Gif'in genişliği ve yüksekliği ayarlayabilirsiniz */
  height: 100px;
}

.spinner {
  border: 12px solid rgba(255, 255, 255, 0.3); /* Kenarları daha kalın yap */
  border-radius: 50%;
  border-top: 12px solid #3498db; /* Spinner'ın üstünü daha kalın yap */
  width: 120px; /* Spinner'ı daha da büyüt */
  height: 120px; /* Spinner'ı daha da büyüt */
  animation: spin 2s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
.disconnect-button {
  transform: translateY(5px); /* 5 piksel aşağı kaydırma */
}


.wallet-balance {
  padding-right: 10px; /* Sağ taraftan 10 piksel padding ekle */
}

.wallet-address {
  padding-left: 10px; /* Sol taraftan 10 piksel padding ekle */
}

/* Tüm metinleri siyah yap */
* {
  color: #000;
}.robotic-text {
  font-family: 'Roboto', sans-serif; /* Robotik bir font kullanabilirsiniz */
  font-size: 14px; /* Boyut ayarınıza göre değiştirin */
  color: #666; /* Gri renk */
}

/* Diğer stiller burada */


.centered-image {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.wallet-info {
  position: absolute;
  top: 10px;
  right: 10px;
  background-color: #f5f5f5;
  padding: 10px;
  border-radius: 5px;
  box-shadow:  0 4px  dimgray, inset  4px 0  dimgray;
  display: flex;
  align-items: center;
}

.wallet-info-content {
  display: flex;
  align-items: center;
  gap: 10px;
}

.wallet-text {
  font-size: 16px;
  margin: 0;
}

.disconnect-button {
  cursor: pointer;
  color: #000;
  text-decoration: underline;
}

.down-arrow {
  font-size: 20px;
  margin-left: 5px;
  color: #000;
}

.wallet-address {
  position: relative;
  display: inline-block;
}

.wallet-address .down-arrow {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  right: 10px;
}

.v-application {
  background-color: #000;
  margin: 0;
  overflow: hidden;
}
</style>
