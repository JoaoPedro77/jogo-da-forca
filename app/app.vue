<template>
  <UApp>
    <MeuHeader :cor-primaria="corPrimaria" />
    <UMain class="bg-stone-800 select-none">
      <!-- ABAS -->
      <UTabs v-model="abaAtiva" :color="corPrimaria" :content="false" :items="items" />
      <!-- TEMA E RESULTADO FIMDE JOGO -->
      <UContainer class="pt-6 gap-2 flex flex-col items-center justify-center">
        <USeparator class=" w-40">
          <template #default>
            <span class="text-xs font-light text-stone-500">{{ minititulo }}</span>
          </template>
        </USeparator>
        <UBadge :icon="iconePersonalizado" size="xl" class="rounded-full"
          :color="minititulo == 'Perdeu...' ? 'tertiary' : corPrimaria" variant="subtle">
          {{ badgePrincipal }}
        </UBadge>
      </UContainer>


      <!-- CONTEÚDO PRINCIPAL -->
      <UContainer class="mb-10 sm:mb-5 flex flex-col gap-5 items-center justify-center">
        <!-- IMAGEM -->
        <img :src="`/desenhos/${forcaTentativa}.png`" class="h-50 sm:h-60 pl-7" />
        <div class="flex flex-wrap justify-center gap-1">
          <div
            :class="{ 'basis-full md:basis-0': (letraPalavra == ' ' || letraPalavra == '-') && (!/\d/.test(palavra[i + 1])) }"
            class="group" v-for="(letraPalavra, i) in palavra" :key="i">
            <UBadge v-if="letravalida(letraPalavra)"
              :color="!chutes.some(l => letraPalavra.includes(l)) ? 'tertiary' : corPrimaria" variant="subtle" class="text-xl font-bold text-white
              w-6 h-6 md:w-12 md:h-12 flex items-center justify-center p-0 ">
              {{chutes.some(l => letraPalavra.includes(l)) ? letraPalavra : ''}}
            </UBadge>
            <div v-if="!letravalida(letraPalavra)" class="w-3 md:w-5 ">
              <span v-if="letraPalavra == `'`" class="w-1 h-2 md:w-1 md:h-3 mb-1 md:mx-1 rounded-2xl bg-stone-400" />
            </div>
          </div>
        </div>

        <!-- botoes -->
        <UButton icon="material-symbols:replay-rounded" @click="tentarNovamente"
          v-if="(ganhoubloqueio || perdeubloqueio) && abaAtiva != 'daily'" :color="corPrimaria" class="text-md font-bold text-white
          px-2 flex items-center  justify-center">
          Jogar Novamente
        </UButton>

        <UButton icon="solar:pen-2-outline" @click="() => { vaiDefinirPalavra = !vaiDefinirPalavra }"
          v-if="!definiuPalavra" :color="corPrimaria" class="text-md font-bold text-white
          px-2 flex items-center justify-center">
          Definir palavra
        </UButton>
      </UContainer>

      <!-- TECLADO -->
      <UContainer class="max-w-[380px] sm:max-w-2xl md:max-w-3xl flex flex-wrap px-1 items-center justify-center">
        <div :class="{ 'basis-full pb-2': letra.letra == ' ' }" v-for="letra in letras" :key="letra.id">

          <UButton @click="chutar(letra)" v-if="letra.letra != ' '" :disabled="desabilitarTeclado(letra.status)"
            :color="corteclado(letra.status)" :class="[
              'text-xl md:text-2xl font-bold text-white flex items-center justify-center p-0',
              /\d/.test(letra.letra) ? 'w-7 h-7 md:w-13 md:h-11 m-0.5 md:m-1' : 'w-8 h-8 md:w-15 md:h-12 m-0.5 xs:m-1',
            ]">
            {{ letra.letra }}
          </UButton>

        </div>
      </UContainer>

      <!-- MODAIS -->
      <UModal :open="ganhou" :close="{ onClick: () => ganhou = false }">
        <template #title>
          <div class="flex items-center justify-center gap-2">
            Parabéns, voce acertou!
            <Icon name="solar:confetti-bold-duotone" />
          </div>
        </template>
        <template #body>
          <div class="flex flex-col gap-2 items-center justify-center">
            <UBadge icon="streamline-plump:balloon-remix" size="xl" class="px-4 rounded-full" :color="corPrimaria"
              variant="subtle">
              A {{ palavraOuFrase }} era: {{ palavra }}
            </UBadge>
            <span>{{ mensagemGameoverVitoria }}</span>
            <img src="/desenhos/7.png" class="h-50" />
            <UButton autofocus="true" icon="material-symbols:replay-rounded" @click="tentarNovamente"
              v-if="(ganhoubloqueio || perdeubloqueio) && abaAtiva != 'daily'" :color="corPrimaria" class="mt-7 text-md font-bold text-white
            px-2 flex items-center justify-center">
              Jogar Novamente
            </UButton>
            <div class="mt-4 flex flex-col items-center justify-center text-stone-500"
              v-if="diariobloqueio && abaAtiva == 'daily'">
              <span class="text-center"> Você completou o desafio diário, volte amanhã para o próximo desafio ou jogue o
                modo
                infinito</span>
              <UButton icon="gravity-ui:thumbs-up-fill" @click="ganhou = false" :color="corPrimaria" class="mt-3 text-md font-bold text-white
              px-2 flex items-center justify-center">
                Entendido!
              </UButton>
            </div>
          </div>
        </template>
      </UModal>

      <UModal :open="perdeu" :close="{ onClick: () => perdeu = false }">
        <template #title>
          <div class="flex items-center justify-center gap-2">
            <Icon name="game-icons:pirate-grave" />
            Que pena, Você perdeu...
          </div>
        </template>
        <template #body>
          <div class="flex flex-col gap-2 items-center justify-center">
            <UBadge icon="streamline-plump:skull-2-solid" size="xl" class="px-4 rounded-full" color="tertiary"
              variant="subtle">
              A {{ palavraOuFrase }} era: {{ palavra }}
            </UBadge>
            <img src="/desenhos/6.png" class="h-50 pl-7" />
            <UButton icon="material-symbols:replay-rounded" @click="tentarNovamente"
              v-if="(ganhoubloqueio || perdeubloqueio) && abaAtiva != 'daily'" :color="corPrimaria" class="mt-7 text-md font-bold text-white
            px-2 flex items-center justify-center">
              Jogar Novamente
            </UButton>
            <div class="mt-4 flex flex-col items-center justify-center text-stone-500"
              v-if="diariobloqueio && abaAtiva == 'daily'">
              <span class="text-center"> Você completou o desafio diário, volte amanhã para o próximo desafio ou jogue o
                modo
                infinito</span>
              <UButton icon="gravity-ui:thumbs-up-fill" @click="perdeu = false" :color="corPrimaria" class="mt-3 text-md font-bold text-white
              px-2 flex items-center justify-center">
                Entendido!
              </UButton>
            </div>
          </div>
        </template>
      </UModal>

      <UModal :open="vaiDefinirPalavra" title="modo personalizado" description="modo personalizado"
        :close="{ onClick: () => vaiDefinirPalavra = false }">
        <template #content>
          <div class="flex flex-col gap-2 items-center justify-center p-5">
            <UBadge size="xl" class="px-4 rounded-2xl text-center" :color="corPrimaria" variant="subtle">
              <Icon name="solar:fire-bold-duotone" />
              MODO PERSONALIZADO
              <Icon name="solar:fire-bold-duotone" />
            </UBadge>
            <span class="text-center mb-4">por favor, defina uma palavra ou frase para começar o jogo!</span>
            <UForm :validate="validate" class="flex flex-col items-center justify-center space-y-1" @submit="onSubmit">
              <UFormField label="Palavra" name="palavra">
                <UInput :color="corPrimaria" v-model="palavra" type="text" class="font-yarndings w-60 sm:w-xs "
                  :size="tamanhoinput" />
              </UFormField>
              <UFormField label="Tema" name="tema">
                <UInput :color="corPrimaria" v-model="tema" type="text" :size="tamanhoinput" class="w-60 sm:w-xs " />
              </UFormField>
              <div class=" space-x-2 md:space-x-4">
                <UButton class="mt-4 text-white" :size="tamanhoinput" label="cancelar" color="tertiary" @click="() => {
                  vaiDefinirPalavra = !vaiDefinirPalavra;
                  palavra = '';
                  if (tema == '' || tema == ' ') tema = 'personalizado'
                }" />
                <UButton class="mt-4 text-white" :size="tamanhoinput" label="Iniciar jogo!" :color="corPrimaria"
                  type="submit" />
              </div>

            </UForm>
          </div>
        </template>
      </UModal>
    </UMain>
  </UApp>
</template>



<script setup>
import { ref } from 'vue';
import { useMediaQuery } from '@vueuse/core'
import { mapeamentoTemas } from '~/utils/mapeamentoTemas.js';
import { apenasLestras, lestrasEnumeros } from '~/utils/letrasTeclado.js';
import { items } from '~/utils/abasForca.js';


// VARIÁVEIS 
const abaAtiva = ref('daily');

const PalavrasForca = ref([]); // palavras do jogo

const iconePersonalizado = ref("");

const palavra = ref("");
const palavraOuFrase = ref("");
const tema = ref("Geral");

let letrasCorretas = [];
let letraQueJaForam = [];

const acertos = ref(0);

const forcaTentativa = ref(0);
const chutes = ref([]);
const erros = ref(0);

const perdeu = ref(false);
const perdeubloqueio = ref(false);
const ganhou = ref(false);
const ganhoubloqueio = ref(false);
const diariobloqueio = ref(false);

const letras = ref(apenasLestras);

const definiuPalavra = ref(true);
const vaiDefinirPalavra = ref(false);

const minititulo = computed(() => {
  if (perdeubloqueio.value) {
    return "Perdeu...";
  } else if (ganhoubloqueio.value) {
    return "Ganhou!!!";
  } else {
    return "Tema";
  }
});

const letravalida = ((letra) => {
  return ![' ', '-', `'`].includes(letra);
});

const desabilitarTeclado = ((status) => {
  return perdeubloqueio.value || ganhoubloqueio.value || palavra.value == '' || status != 'padrao';
});

const badgePrincipal = computed(() => {
  if (perdeubloqueio.value) {
    return `A ${palavraOuFrase.value} era: ${palavra.value}`;
  } else if (ganhoubloqueio.value) {
    return "Parabéns !!!";
  } else {
    return tema.value;
  }
});

const mensagemGameoverVitoria = computed(() => {
  switch (erros.value) {
    case 0:
      return "Você não errou nenhuma letra!";
    case 1:
      return "Você errou apenas 1 letra, muito bom!";
    case 2:
      return `Você errou só 2 letras, boa!`;
    case 3:
      return `Você errou 3 letras.`;
    case 4:
      return `Vish, você errou 4 letras.`;
    case 5:
      return `Você errou 5 letras, por pouco!`;
    default:
      return "";
  }
});

const corteclado = ((status) => {
  switch (status) {
    case 'padrao':
      return corPrimaria.value;
    case 'tem':
      return 'success';
    case 'naoTem':
      return 'tertiary';
  }
});

const corPrimaria = computed(() => {
  const cores = ["primary", "red", "orange", "emerald", "miku", "blue", "indigo", "violet", "purple", "pink", "secondary"];
  return cores[Math.floor(Math.random() * cores.length)];
});


// ABAS ---------------------------------------------------------------------------------------

watch(abaAtiva, (novoValor) => {
  switch (novoValor) {
    case 'infinity':
      tentarNovamente();
      vaiDefinirPalavra.value = false;
      definiuPalavra.value = true;
      break;

    case 'daily':
      tentarNovamente()
      vaiDefinirPalavra.value = false;
      definiuPalavra.value = true;
      carregarPalavraDiaria();
      carregarTentativaDiaria();
      break;

    case 'party':
      tentarNovamente();
      tema.value = "Personalizado";
      break;

  }
});


// FUNÇÕES ---------------------------------------------------------------------------------------

function carregarPalavraDiaria() {
  const dataAtual = new Date();
  const diaDoAno = Math.floor((dataAtual - new Date(dataAtual.getFullYear(), 0, 0)) / 1000 / 60 / 60 / 24);
  const indicePalavra = diaDoAno % PalavrasForca.value.length;
  const palavraSorteada = PalavrasForca.value[indicePalavra];
  palavra.value = palavraSorteada.palavra;
  tema.value = palavraSorteada.tema;
  formatarPalavra();
  carregarLetrasCorretas();

}

//tira os numeros
function resetarLetras() {
  letras.value = apenasLestras;
}

//poe as letras corretas na array letrasCorretas
function carregarLetrasCorretas() {
  for (let p in palavra.value) {
    if (!(letrasCorretas.includes(palavra.value[p])) && palavra.value[p] != ' ' && palavra.value[p] != '-' && palavra.value[p] != `'`) {
      letrasCorretas.push(palavra.value[p])
    }
  }
}

//verifica se tem as letras que foram chutadas
function chutar(letra) {
  //se tiver a letra ou algumva variação dela
  for (let lc in letrasCorretas) {

    if (letra.alt.includes(letrasCorretas[lc])) {
      chutes.value.push(letrasCorretas[lc])
      letra.status = "tem"
      acertos.value += 1;

      if (acertos.value === letrasCorretas.length) {
        ganhou.value = true
        ganhoubloqueio.value = true
        diariobloqueio.value = true
        forcaTentativa.value = 7
        iconePersonalizado.value = "solar:confetti-bold-duotone"
      }
      if (abaAtiva.value == 'daily') { salvarTentativaDiaria() }
    }
  }

  //se n tiver a letra
  if (letra.status != "tem") {
    erros.value += 1
    letra.status = "naoTem"
    forcaTentativa.value += 1
    if (forcaTentativa.value === 6) {
      perdeu.value = true
      perdeubloqueio.value = true
      diariobloqueio.value = true

      iconePersonalizado.value = "streamline-plump:skull-2-solid"
    }
    if (abaAtiva.value == 'daily') { salvarTentativaDiaria() }

  }
}

function carregarPalavraAleatoria() {
  const palavraSorteada = PalavrasForca.value[Math.floor(Math.random() * PalavrasForca.value.length)];
  palavra.value = palavraSorteada.palavra;
  tema.value = palavraSorteada.tema;

}

// gera a palavra da vez
function formatarPalavra() {
  palavra.value = palavra.value.toUpperCase();
  palavraOuFrase.value = palavra.value.includes(' ') ? "frase" : "palavra";
  if (/\d/.test(palavra.value)) {
    letras.value = lestrasEnumeros;
  }
}

function resetarStatusTeclado() {
  for (let i in letras.value) {
    letras.value[i].status = "padrao";
  }
}

//reinicia valores pra tentar novamente.
function tentarNovamente() {
  letraQueJaForam = [];
  acertos.value = 0;
  forcaTentativa.value = 0;
  chutes.value = [];
  perdeu.value = false;
  perdeubloqueio.value = false;
  ganhou.value = false;
  ganhoubloqueio.value = false;
  iconePersonalizado.value = gerarIconePorTema(tema);
  letrasCorretas = [];
  erros.value = 0;

  if (abaAtiva.value == 'daily') {
    return;
  }

  if (abaAtiva.value == 'party') {
    vaiDefinirPalavra.value = false;
    definiuPalavra.value = false;
    palavra.value = '';
  }

  if (abaAtiva.value == 'infinity') {
    carregarPalavraAleatoria();
  }

  resetarLetras();
  resetarStatusTeclado();
  formatarPalavra();
  carregarLetrasCorretas();
}

function gerarIconePorTema(tema) {
  const nomeTema = tema.value.toLowerCase().trim();
  return mapeamentoTemas[nomeTema] || 'solar:pen-new-square-linear';
}

watchEffect(() => {
  iconePersonalizado.value = gerarIconePorTema(tema);
});


// FORMS ---------------------------------------------------------------------------------------

const validate = () => {
  const errors = []

  if (!palavra.value) {
    errors.push({ name: 'palavra', message: 'Por favor digite uma palavra ou frase' })
  } else {
    const palavraLimpa = palavra.value.trim()
    const regexValida = /^[a-zA-ZÀ-ÿ0-9\s\-']+$/
    if (palavraLimpa.length < 2) {
      errors.push({ name: 'palavra', message: 'Digite pelo menos 2 caracteres' })
    } else if (!regexValida.test(palavraLimpa)) {
      errors.push({
        name: 'palavra',
        message: 'Não use caracteres especiais.'
      })
    }
  }

  if (!tema.value || tema.value.trim().length === 0) {
    errors.push({ name: 'tema', message: 'Por favor insira o tema' })
  } else if (tema.value.trim().length < 2) {
    errors.push({ name: 'tema', message: 'O tema deve ter pelo menos 2 caracteres' })
  }

  return errors
}

async function onSubmit() {
  vaiDefinirPalavra.value = !vaiDefinirPalavra.value
  definiuPalavra.value = true;
  formatarPalavra();
  carregarLetrasCorretas();

}

//responsivo

const isLarge = useMediaQuery('(min-width: 1024px)')
const isMedium = useMediaQuery('(min-width: 768px)')

const tamanhoinput = computed(() => {
  if (isLarge.value) return 'xl'
  if (isMedium.value) return 'lg'
  return 'md'
})


// teclado fisico

function encontrarLetraPorTecla(teclaPressionada) {
  const tecla = teclaPressionada.toUpperCase();

  return letras.value.find(letra =>
    letra.alt.includes(tecla) || //maldito ç, morra ç
    letra.letra === tecla
  );
}

function handler(event) {
  if ((letraQueJaForam.includes(event.key))) {
    return;
  }
  if (perdeubloqueio.value || ganhoubloqueio.value || !definiuPalavra.value || vaiDefinirPalavra.value) {
    return
  }
  letraQueJaForam.push(event.key);
  if ((event.key.length === 1 && /[a-zA-ZÀ-ÿ-0-9]/.test(event.key))) {
    event.preventDefault();
    const letraEncontrada = encontrarLetraPorTecla(event.key);
    if (letraEncontrada) {
      chutar(letraEncontrada);
    }
  }
}


// SALVAR E CARREGAR ---------------------------------------------------------------------------------------

function salvarTentativaDiaria() {

  try {
    localStorage.setItem('tentativaDiaria', JSON.stringify({
      palavra: palavra.value,
      tema: tema.value,
      chutes: chutes.value,
      forcaTentativa: forcaTentativa.value,
      acertos: acertos.value,
      erros: erros.value,
      perdeu: perdeu.value,
      ganhou: ganhou.value,
      letras: letras.value,
      letraQueJaForam: letraQueJaForam,
      bloqueios: {
        perdeubloqueio: perdeubloqueio.value,
        ganhoubloqueio: ganhoubloqueio.value,
        diariobloqueio: diariobloqueio.value,
      }
    }));
  }
  catch (e) {
    console.warn('deu problemas', e);
  }
}

function carregarTentativaDiaria() {
  const tentativaSalva = JSON.parse(localStorage.getItem('tentativaDiaria'));
  if ((tentativaSalva) && abaAtiva.value == 'daily') {
    chutes.value = tentativaSalva.chutes;
    forcaTentativa.value = tentativaSalva.forcaTentativa;
    acertos.value = tentativaSalva.acertos;
    erros.value = tentativaSalva.erros;
    letras.value = tentativaSalva.letras;
    diariobloqueio.value = tentativaSalva.bloqueios.diariobloqueio;
    perdeubloqueio.value = tentativaSalva.bloqueios.perdeubloqueio;
    ganhoubloqueio.value = tentativaSalva.bloqueios.ganhoubloqueio;
    letraQueJaForam = tentativaSalva.letraQueJaForam;
  }
  carregarLetrasCorretas();
}


// LIFECYCLE HOOKS ---------------------------------------------------------------------------------------

onMounted(async () => {
  await fetch('/palavras_forca.json')
    .then(res => res.json())
    .then(data => {
      PalavrasForca.value = data;
    });
  carregarPalavraDiaria();
  if (abaAtiva.value == 'daily') carregarTentativaDiaria();
  // detecta o teclado físico
  document.addEventListener('keypress', handler);
})

onUnmounted(() => document.removeEventListener('keypress', handler))
</script>