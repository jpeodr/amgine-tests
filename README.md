# Máquina Enigma Virtual - Projeto AMGINE

Uma simulação interativa e funcional da famosa **Máquina Enigma**, historicamente utilizada durante a Segunda Guerra Mundial para criptografia militar. Este projeto recria precisamente o mecanismo de funcionamento da máquina de forma virtual e acessível na web.

**[Acesse a simulação aqui](https://jpeodr.github.io/amgine-tests/)**

---

## 📋 Sobre o Projeto

A Máquina Enigma era um dispositivo eletromecânico que realizava substituição polialfabética de letras. Esta implementação virtual mantém a essência do funcionamento original, oferecendo uma experiência interativa que demonstra como o criptossistema funcionava.


## 🔐 Como Funciona

### Mecanismo de Criptografia

A Máquina Enigma opera através de um processo de **substituição de caracteres** que muda a cada letra digitada:

#### 1. **Os Três Rotores**

Cada rotor é um array de 26 posições (uma para cada letra do alfabeto) que funciona como uma tabela de substituição:

```javascript
// Exemplo de rotor
vogaisTrocada1 = [5, 20, 19, 8, 7, 0, 21, 4, 3, 23, 11, 10, 25, 24, 15, 14, 22, 18, 17, 2, 1, 6, 16, 9, 13, 12];
```

Se você digita a letra na posição 0 (letra 'A'), o rotor 1 direciona para a posição 5 (letra 'F').

#### 2. **Fluxo de Criptografia (Ida)**

Quando você pressiona uma tecla, a letra passa por cada rotor sequencialmente:

```
Letra digitada (entrada) 
    ↓
Rotor 1 (1ª substituição)
    ↓
Rotor 2 (2ª substituição)
    ↓
Rotor 3 (3ª substituição)
    ↓
Refletor (ponto de reversão)
```

O **refletor** é um array especial que realiza a última transformação:

```javascript
vogaisTrocadaRefletor = [9, 11, 8, 22, 14, 7, 21, 5, 2, 0, 20, 1, 17, 16, 4, 25, 13, 12, 23, 24, 10, 6, 3, 18, 19, 15];
```

#### 3. **Fluxo de Criptografia (Volta)**

Após o refletor, o sinal retorna pelos rotores em ordem inversa:

```
Refletor (transformação central)
    ↓
Rotor 3 (busca inversa)
    ↓
Rotor 2 (busca inversa)
    ↓
Rotor 1 (busca inversa)
    ↓
Letra cifrada (saída)
```

#### 4. **Rotação Automática dos Rotores**

Após cada letra processada, os rotores giram como um odômetro:

- **Rotor 1** gira após cada letra
- **Rotor 2** gira quando o Rotor 1 completa 26 rotações
- **Rotor 3** gira quando o Rotor 2 completa 26 rotações

```javascript
function rotateRotorsAfterInput() {
    if (rotor1Posicao < 25) {
        rotor1Posicao++;
        // Rotores rotacionam (arrays são deslocados)
    } else {
        rotor1Posicao = 0;
        rotor2Posicao++;
        // Cascata para rotores maiores...
    }
}
```

### Exemplo Prático

Se você digitar a sequência **"AAA"**:

1. **1º 'A':** Com os rotores em posição inicial, pode produzir **'H'**
2. **2º 'A':** Após o Rotor 1 girar, pode produzir **'M'**
3. **3º 'A':** Após o Rotor 1 girar novamente, pode produzir **'E'**

A mesma letra sempre produz saídas diferentes graças à rotação automática dos rotores.

### Propriedade Recíproca

A máquina possui uma propriedade única: **se você criptografar uma mensagem e depois digitar o resultado para criptografar novamente, obterá a mensagem original!**

Mensagem: `TESTE` → Cifrado: `VJGGQ`  
Cifrado: `VJGGQ` → Descriptografado: `TESTE`

---

## 🛠️ Como Foi Construída

### Tecnologias Utilizadas

| Tecnologia | Uso |
|-----------|-----|
| **HTML5** | Estrutura da interface e elementos DOM |
| **CSS3** | Estilização visual realista e responsividade |
| **JavaScript Vanilla** | Lógica de criptografia e interatividade |

### Arquitetura Geral

#### **Estrutura HTML**

```html
<div class="enigma-container">
  <!-- Seção de rotores com controles -->
  <div class="rotor-section">
    <!-- 3 rotores + knob seletor -->
  </div>
  
  <!-- Painel de LEDs (saída visual) -->
  <div class="led-panel">
    <!-- 26 LEDs representando as letras -->
  </div>
  
  <!-- Teclado (entrada) -->
  <div class="keyboard-section">
    <!-- 26 teclas (layout QWERTZ) -->
  </div>
  
  <!-- Displays de entrada/saída -->
  <div class="message-section">
    <!-- Campos de texto para mensagens -->
  </div>
  
  <!-- Botões de controle -->
  <div class="controls">
    <!-- LIMPAR e RESET -->
  </div>
</div>
```

#### **Estrutura de Dados JavaScript**

Os rotores são implementados como arrays de permutação:

```javascript
// Arrays dos 3 rotores (permutações de 0-25)
let vogaisTrocada1 = [5, 20, 19, ...];  // Rotor mais rápido
let vogaisTrocada2 = [20, 8, 11, ...];  // Rotor intermediário
let vogaisTrocada3 = [9, 2, 1, ...];    // Rotor mais lento

// Refletor (ponto de reversão)
let vogaisTrocadaRefletor = [9, 11, 8, ...];

// Posições atuais dos rotores
let rotor1Posicao = 0;  // 0-25
let rotor2Posicao = 0;  // 0-25
let rotor3Posicao = 0;  // 0-25
```

#### **Fluxo de Processamento**

```
[Usuário pressiona tecla]
           ↓
[processLetter(letra)]
           ↓
[Processa pela lógica Enigma]
           ↓
[Acende LED + Anima teclado]
           ↓
[Rotaciona rotores]
           ↓
[Atualiza displays]
```

### Funções Principais

#### 1. `processLetter(letra)`
Processa uma letra através de toda a máquina:
- Encontra a posição inicial da letra
- Passa pelo Rotor 1, 2, 3
- Passa pelo Refletor
- Retorna pelos Rotor 3, 2, 1
- Acende o LED correspondente
- Armazena a saída
- Rotaciona os rotores

#### 2. `rotateRotorsAfterInput()`
Implementa a lógica de cascata dos rotores (odômetro):
- Incrementa Rotor 1 após cada letra
- Cascata para Rotor 2 a cada 26 letras
- Cascata para Rotor 3 a cada 676 letras

#### 3. `rotacionaArray(arr)` / `rotacionaArrayInverso(arr)`
Realiza a rotação física dos rotores:
- Move cada elemento um passo à frente/atrás
- Funciona como um cilindro rotativo

#### 4. `adjustRotor(rotorNum, direction)`
Permite ajuste manual dos rotores:
- Usuário pode girar qualquer rotor com botões ▲ e ▼
- Essencial para configurar a posição inicial (chave)

#### 5. `lightLED(letra)` / `animateKeyPress(letra)`
Fornece feedback visual ao usuário:
- Acende o LED correspondente à letra cifrada
- Anima a tecla do teclado

### Design Responsivo

O projeto utiliza media queries para adaptar-se a diferentes telas:

- **Desktop** (> 640px): Layout completo e espaçado
- **Tablet** (480-640px): Elementos reduzidos, mantendo funcionalidade
- **Mobile** (< 480px): Componentes compactados, suporte para toque

### Suporte Multi-dispositivo

- **Teclado Físico**: Detecta teclas pressionadas e processa automaticamente
- **Dispositivos Móveis**: Implementação de `touchstart` para simular cliques em elementos

```javascript
// Toque em dispositivos móveis
element.addEventListener('touchstart', function(event) {
    event.preventDefault();
    element.click();  // Dispara o evento de clique
});
```

---

## 🎮 Como Usar

### Interface

#### **Rotores**
- **Janelas superiores**: Mostram a posição atual de cada rotor (A-Z)
- **Botões ▲ e ▼**: Ajustam manualmente a posição de cada rotor
- **Posição inicial é a "chave"**: Configura como a máquina criptografará a mensagem

#### **Teclado**
- Clique nas 26 letras (ou use teclado físico)
- Cada letra é processada e aparece no campo "Entrada da Mensagem"

#### **Painel de LEDs**
- Acende quando a letra é criptografada
- Brilha por 1 segundo, depois apaga

#### **Mensagens**
- **Entrada**: Mostra as letras digitadas
- **Saída**: Mostra as letras criptografadas

#### **Botões de Controle**
- **LIMPAR**: Apaga todas as mensagens e LEDs
- **RESET**: Retorna os rotores à posição inicial (A A A)

### Modo de Uso Típico

#### **Para Criptografar uma Mensagem**

1. Configure a chave:
   - Clique nos botões dos rotores para definir uma posição inicial
   - Anote: ex. "K V D" (essa é sua chave secreta)

2. Digite a mensagem:
   - Clique nas letras do teclado ou use seu teclado
   - Observe a mensagem cifrada na saída

3. Compartilhe o resultado:
   - Envie a mensagem criptografada para alguém
   - Compartilhe também a chave (posição dos rotores)

#### **Para Descriptografar uma Mensagem**

1. Receba a mensagem criptografada e a chave

2. Configure a mesma chave:
   - Coloque os rotores na posição inicial da chave

3. Digite a mensagem cifrada:
   - A saída será a mensagem original!


---

## 💡 Notas Técnicas

### Configurações dos Rotores

Os rotores foram configurados baseando-se em implementações históricas e validados para funcionar corretamente:

```javascript
// Rotor 1 (Umkehrwalze I)
vogaisTrocada1 = [5, 20, 19, 8, 7, 0, 21, 4, 3, 23, 11, 10, 25, 24, 15, 14, 22, 18, 17, 2, 1, 6, 16, 9, 13, 12];

// Rotor 2 (Umkehrwalze II)
vogaisTrocada2 = [20, 8, 11, 18, 12, 22, 23, 21, 1, 24, 17, 2, 4, 15, 25, 13, 19, 10, 3, 16, 0, 7, 5, 6, 9, 14];

// Rotor 3 (Umkehrwalze III)
vogaisTrocada3 = [9, 2, 1, 17, 22, 24, 16, 21, 15, 0, 23, 20, 14, 19, 12, 8, 6, 3, 25, 13, 11, 7, 4, 10, 5, 18];

// Refletor (UKW)
vogaisTrocadaRefletor = [9, 11, 8, 22, 14, 7, 21, 5, 2, 0, 20, 1, 17, 16, 4, 25, 13, 12, 23, 24, 10, 6, 3, 18, 19, 15];
```

### Propriedades Matemáticas

A máquina Enigma tem uma propriedade notável:

- **Involutória**: A mesma configuração criptografa E descriptografa
- **Determinística**: Mesma entrada + mesma chave = mesma saída
- **Acíclica em ciclos curtos**: Não repete a mesma passagem em até 26³ caracteres

### Compatibilidade

- ✅ Chrome/Chromium
- ✅ Firefox
- ✅ Safari
- ✅ Edge
- ✅ Navegadores móveis (iOS Safari, Chrome Mobile)

---

## 📱 Responsividade

O projeto foi otimizado para múltiplas resoluções:

- **Desktop (1920px+)**: Experiência completa e espaçada
- **Laptop (1024-1920px)**: Layout adaptado
- **Tablet (640-1024px)**: Elementos redimensionados
- **Mobile (480-640px)**: Componentes compactados
- **Smartphone (< 480px)**: Layout mobile otimizado

---

## 🎓 Aspecto Educacional

Este projeto é excelente para:

- Entender a criptografia clássica
- Aprender sobre máquinas eletromecânicas
- Explorar a história da Segunda Guerra Mundial
- Compreender mecanismos de substituição polialfabética
- Visualizar como rotações e permutações funcionam em criptografia

---

## 📜 Referências Históricas

A máquina Enigma foi desenvolvida por Arthur Scherbius em 1918 e amplamente utilizada na Alemanha Nazi durante a WWII. Sua quebra pelos matemáticos da Bletchley Park (incluindo Alan Turing) foi crucial para os Aliados.

Este projeto recria fielmente o mecanismo básico da Enigma-I, a versão mais comum usada durante a guerra.

---

## 🔗 Acesse Agora

**[Clique aqui para usar a Máquina Enigma Virtual](https://jpeodr.github.io/amgine-tests/)**

---

*Projeto AMGINE - Simulação educacional da Máquina Enigma*
