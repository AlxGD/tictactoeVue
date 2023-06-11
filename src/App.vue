<template>
  <div id="app">
    <h1 class="titulo">Jogo da Velha</h1>
    <div class="tabuleiro">
      <div
          class="card"
          v-for="(cell, index) in cards"
          :key="index"
          @click="fazerMovimento(index)"
      >
        <span :class="['simbolos', cell]">{{ cell }}</span>
      </div>
    </div>
    <button class="botaoReiniciar" @click="reiniciarJogo">Reiniciar</button>

    <teleport to="body">
      <transition name="fade">
        <div v-if="mostrarPopup" class="popupFimDeJogo">
          <h2 v-if="vencedor" class="popupVitoria">
            Parabéns!! {{ vencedor }} venceu!
          </h2>
          <h2 v-else class="popupVitoria">Empate! :o</h2>
          <button @click="esconderPopup" class="popupBotao">Jogar Novamente</button>
        </div>
      </transition>
    </teleport>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cards: Array(9).fill(""),
      jogadorAtual: "X",
      vencedor: null,
      mostrarPopup: false,
    };
  },
  computed: { //para verificar se o jogo foi um empate
    aconteceuEmpate() {
      return !this.vencedor && this.cards.every((cell) => cell !== "");
      //!this.vencedor é o oposto de deuqme venceu. coloquei isso pra checar se tem vencedor
      // a condicao checa tbm se tá tudo ocupado
    },
  },
  methods: {
    fazerMovimento(index) {
      if (!this.cards[index] && !this.vencedor) {
        this.cards.splice(index, 1, this.jogadorAtual);
        this.verificarVencedor();
        this.jogadorAtual = this.jogadorAtual === "X" ? "O" : "X"; //indicando a troca de turnos
      }
    },
    verificarVencedor() {
      const combinacoesVitoriosas = [ //todas as possibilidades de vitórias (essa parte foi chata)
        [0, 1, 2],
        [3, 4, 5],
        [6, 7, 8],
        [0, 3, 6],
        [1, 4, 7],
        [2, 5, 8],
        [0, 4, 8],
        [2, 4, 6],
      ];

      for (let combinacoes of combinacoesVitoriosas) {
        const [a, b, c] = combinacoes;
        if (
            this.cards[a] &&
            this.cards[a] === this.cards[b] &&
            this.cards[a] === this.cards[c]
        ) {
          this.vencedor = this.jogadorAtual;
          this.mostrarPopup = true;
          break;
        }
      }

      if (this.aconteceuEmpate) {
        this.mostrarPopup = true;
      }
    },
    reiniciarJogo() {
      this.cards = Array(9).fill("");
      this.jogadorAtual = "X";
      this.vencedor = null;
      this.mostrarPopup = false;
    },
    esconderPopup() {
      this.reiniciarJogo();
    },
  },
};
</script>

<style>
.fadeInPopup, .fadeOutPopup {
  transition: opacity 0.5s;
}

.fadeIn, .fadeOut {
  opacity: 0;
}

.simbolos {
  font-family: Helvetica, Arial, sans-serif;
  font-weight: bold;
}

.X {
  color: #da5959;
}

.O {
  color: #4b4ba9;
}

.popupVitoria {
  font-family: Helvetica, Arial, sans-serif;
  font-weight: bold;
  color: #cecece;
}

.titulo {
  text-align: center;
  font-family: Helvetica, Arial, sans-serif;
  font-size: 2.5rem;
  color: #333;
  margin-bottom: 20px;
}

.botaoReiniciar {
  padding: 10px 20px;
  font-size: 1.2rem;
  border-radius: 5px;
  background-color: #4caf50;
  color: white;
  border: none;
  cursor: pointer;
  margin: 10px auto;
  display: block;
  font-family: Helvetica, Arial, sans-serif;
}

.tabuleiro {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 10px;
  margin-bottom: 10px;
}

.card {
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 3rem;
  background-color: #f7f7f7;
  cursor: pointer;
  height: 100px;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: background-color 0.3s;
}

.card:hover {
  background-color: #e6e6e6;
}

button {
  padding: 10px 20px;
  font-size: 1rem;
  border-radius: 5px;
  background-color: #4caf50;
  color: white;
  border: none;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #45a049;
}

.popupFimDeJogo {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  z-index: 9999;
  color: white;
  font-size: 2rem;
}

.popupFimDeJogo h2 {
  margin-bottom: 20px;
}

.popupBotao {
  padding: 10px 20px;
  font-size: 1rem;
  border-radius: 5px;
  background-color: #4caf50;
  color: white;
  border: none;
  cursor: pointer;
  transition: background-color 0.3s;
}

.popupBotao:hover {
  background-color: #45a049;
}

</style>
