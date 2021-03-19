## Installing

Ambiente python3.

Install and update using [pip](https://github.com/jefrysastre/dsbot#installing):

* `pip install -U dsbot`


## Study

Nosso estudo consiste em estender um framework para criar chatbots, mas antes de tudo vamos entender o tutorial da ferramenta [dsbot](https://github.com/jefrysastre/dsbot)

Crie um chat bot capaz de executar as seguintes tarefas utilizado a ferramenta **dsbot**. 
Para criar os commandos novos vc tem que estender a ferramenta. Para estender a ferramenta siga os exemplos do link: [Tutorial](https://github.com/jefrysastre/dsbot#creating-new-commands)

* Crie um comando para mostrar o cabeçalho do dataset (as 5 primeiras linhas). Pode usar a funcão head do pandas para mostrar as linhas.
* Aplique uma transformação logaritmica na primeira coluna. Pode usar a função np.log do pacote numpy.
* Carrege o dataset iris e teste os comandos que vc implementou. Para carregar o dataset escreva algo do tipo: _vamos carregar o dataset iris_