# DCA0445 - PROCESSAMENTO DIGITAL DE IMAGENS
## 1º Unidade
### 1.1 Manipulando Pixels em uma Imagem - Regiões

O programa **pixels.cpp** foi utilizado como referência para o desenvolvimento de um programa chamado **regions.cpp**. O objetivo desse novo programa é solicitar ao usuário as coordenadas de dois pontos **P1** e **P2** localizados dentro dos limites do tamanho da imagem fornecida. Os pontos escolhidos formam uma região retangular de vértices opostos, e a parcela da imagem comprendida na parte interna a esse retângulo é exibida com o negativo. 

**pixels.cpp**
```cpp

#include <iostream>
#include <opencv2/opencv.hpp>

using namespace cv;
using namespace std;

int main(int, char**){
  cv::Mat image;
  cv::Vec3b val;

  image= cv::imread("bolhas.png",cv::IMREAD_GRAYSCALE);
  if(!image.data)
    std::cout << "nao abriu bolhas.png" << std::endl;

  cv::namedWindow("janela", cv::WINDOW_AUTOSIZE);

  for(int i=200;i<210;i++){
    for(int j=10;j<200;j++){
      image.at<uchar>(i,j)=0;
    }
  }
  
  cv::imshow("janela", image);  
  cv::waitKey();

  image= cv::imread("bolhas.png",cv::IMREAD_COLOR);

  val[0] = 0;   //B
  val[1] = 0;   //G
  val[2] = 255; //R
  
  for(int i=200;i<210;i++){
    for(int j=10;j<200;j++){
      image.at<Vec3b>(i,j)=val;
    }
  }

  cv::imshow("janela", image);  
  cv::waitKey();
  return 0;
}

```
O programa **regions.cpp** foi desenvolvido usando a imagem: **Bob.png** como referência e gerou a imagem: **Resultado de regions.png**. Os pontos usados para formar o retângulo de nagativo foram: **P1 = (100, 200)** e **P2 = (300, 460)** e a dimensão dessas duas imagens é: **600x803**.

**regions.cpp**
```cpp

#include <opencv2/opencv.hpp>
#include <iostream>
using namespace cv;
using namespace std;

/* A função recebimento(), realiza a solicitação de dois pontos ao usuário
e retorna um vetor com esses dois pontos recebidos. O superior esquerdo na pos. 0
e o inferior direito na posição 1.
*/

vector<Point> recebimento(){
    Point p1, p2; /* Esses são os pontos fornecidos pelo usuário. Inicialmente 
    		     não tem como saber qual é o superior esquerdo ou inferior 
    		     direito
    		  */
    vector<Point> p; // Armazena os pontos na ordem correta
    cout<<"Digite as coordenadas (X, Y) do primeiro ponto: ";
    cin>>p1.x>>p1.y;
    cout<<"Digite as coordenadas (X, Y) do segundo ponto: ";
    cin>>p2.x>>p2.y;

    if(p1.x < p2.x){ 		// Caso p1 seja o ponto superior esquerdo
        p.push_back(p1);
        p.push_back(p2);
    } else{ 			// Caso p1 seja o ponto inferior direito
        p.push_back(p2);
        p.push_back(p1);
    }
    return p;
} 			      

int main(){
    cv::Mat imagem;
    vector<Point> pontos;
    Point ini, fim; 
    imagem = cv::imread("Bob.png",cv::IMREAD_GRAYSCALE); // Carrega a imagem Bob.png
    pontos = recebimento();
    ini = pontos[0]; fim = pontos[1];

    cout<<"Ponto superior esquerdo:\n";
    cout<<ini<<endl;
    cout<<"Ponto inferior direito:\n";
    cout<<fim<<endl;

    for(int i=ini.x; i<fim.x; i++){
        for(int j=ini.y; j<fim.y; j++){
            imagem.at<uchar>(j,i) = 255 - imagem.at<uchar>(j,i);
        }
    }
    imshow("Resultado do Processamento", imagem);
    waitKey();

    return 0;
}

```

![Bob](https://user-images.githubusercontent.com/37122281/96937626-f4d63780-149e-11eb-80ca-23c219d06043.png)

**Bob.png**

![Resultado de regions](https://user-images.githubusercontent.com/37122281/96938260-3c10f800-14a0-11eb-9cd2-db924f98aa4c.png)

**Resultado de regions.png**

### 1.2 Manipulando Pixels em uma Imagem - Troca de quadrantes em Diagonal
O programa chamado: **trocaregioes.cpp** troca os quadrantes em diagonal na imagem. Esse resultado foi obtido usando uma das implementações do construtor da classe Mat que permite definir uma imagem como uma região de outra já existente. Um exemplo disso é o comando: **Mat S_esquer(imagem, Rect(0,0, imagem/2, imagem/2))**. Essa linha de código cria a matriz **S_esquer** e coloca nela o conteúdo da **imagem original** que está dentro do retângulo passado. No OpenCV retângulos podem ser definidos como **Rect(coordX, coordY, largura, altura)**. A imagem antes do processamento é a i**magem_original.png** e após é a **resultado_de_trocaregioes.png**.

**trocaregioes.cpp**
```cpp

#include <iostream>
#include <opencv2/opencv.hpp>
using namespace std;
using namespace cv;

int main(){
    cv::Mat imagem;
    imagem = cv::imread("Bob.png",cv::IMREAD_GRAYSCALE); // Carrega a imagem Bob.png
    /*	Declaração de um retângulo: Rect retangulo(x,y, largura, altura);
       Declarando as regiões da imagem original
    */
    
    Mat resultado = imagem.clone();  //imagem é clonado para resultado, para apresentar o mesmo tamanho
    Mat S_esquer(imagem, Rect(0,0, imagem.cols/2, imagem.rows/2)); // Região superior esquerda
    Mat S_dir(imagem, Rect(imagem.cols/2, 0, imagem.cols/2, imagem.rows/2)); // Região superior direita
    Mat I_esquer(imagem, Rect(0, imagem.rows/2, imagem.cols/2, imagem.rows/2)); // Região inferior esquerda
    Mat I_dir(imagem, Rect(imagem.cols/2, imagem.rows/2, imagem.cols/2, imagem.rows/2)); // Região inferior direita


    I_dir.copyTo(resultado(Rect(0, 0, I_dir.cols, I_dir.rows))); // I_dir para S_esquer
    S_esquer.copyTo(resultado(Rect(imagem.cols/2, imagem.rows/2, S_esquer.cols, S_esquer.rows))); // S_esquer para I_dir
    I_esquer.copyTo(resultado(Rect(imagem.cols/2, 0, I_esquer.cols, I_esquer.rows))); // I_esquer -> S_dir
    S_dir.copyTo(resultado(Rect(0, imagem.rows/2, S_dir.cols, S_dir.rows))); // S_dir -> I_esquer

    imshow("Imagem Original", imagem);
    imshow("Regiões Trocadas", resultado);
    waitKey();
    
    return 0;
}

```

![Imagem Original](https://user-images.githubusercontent.com/37122281/96947152-c5ccbf80-14b8-11eb-96a8-becaddb0097e.png)

**imagem_original.png**

![Resultado de trocaregioes](https://user-images.githubusercontent.com/37122281/96947229-03314d00-14b9-11eb-96b1-87615b25a27c.png)

**resultado_de_trocaregioes.png**

### 2.1 Preenchendo Regiões - Contagem de Bolhas e Buracos

O **algoritmo de contagem apresentado (labeling.cpp)** foi aprimorado para identificar regiões com ou sem buracos internos que existem na cena. Foi assumido que objetos com mais de um buraco podem existir e o algoritmo não conta bolhas que tocam as bordas da imagem. **Foram encontradas 21 bolhas, sendo 7 com buraco**. As imagens geradas para fazer o processamento (**bolhas.png, buracos.png, result_Bolhas.png, sem_borda.png**) serão exibidas abaixo.

**labeling.cpp**
```cpp

#include <opencv2/opencv.hpp>
#include <iostream>
#include <stack>
using namespace std;
using namespace cv;

 
void floodfill(Mat im, cv::Point p, int cor){
    stack<cv::Point> pilha;
    pilha.push(p); 
    int corAnt = im.at<uchar>(p);
    cv::Point temp; // Representa sempre o que está sendo processado.

    while(!pilha.empty()){ // Atualiza pilha
        temp = pilha.top(); // Recupera  topo
        pilha.pop(); // Remove topo

        if (im.at<uchar>(temp)==corAnt){
            im.at<uchar>(temp) = cor; // Recebe a nova cor
            if(temp.x >= 1)        pilha.push(cv::Point(temp.x-1, temp.y)); // Vizinho de cima
            if(temp.x+1 < im.rows) pilha.push(cv::Point(temp.x+1, temp.y)); // Vizinho de baixo
            if(temp.y >= 1)        pilha.push(cv::Point(temp.x,   temp.y-1)); // Vizinho da esquerda
            if(temp.y+1 < im.cols) pilha.push(cv::Point(temp.x,   temp.y+1)); // Vizinho da direita
        }
    }
}

/*
   Retira bolhas da bordas
 */
void filtraBordas(Mat im){
    for(int c=0; c<im.cols; c++){ // Percorre borda superior
        im.at<uchar>(0, c) = 255;
    }
    for(int c=0; c<im.cols; c++){ // Percorre borda inferior
        im.at<uchar>(im.cols-1, c) = 255;
    }
    for(int l=0; l<im.rows; l++){ // Percorre borda esquerda
        im.at<uchar>(l, 0) = 255;
    }
    for(int l=0; l<im.rows; l++){ // Percorre borda direita
        im.at<uchar>(l, im.rows-1) = 255;
    }
    floodfill(im, cv::Point(0,0), 0); // A borda de 'im' é pintada de preto 
}

int main(){
    
    cv::Mat imagem;
    cv::Mat original;
    imagem = cv::imread("bolhas.png",cv::IMREAD_GRAYSCALE); // Carrega a imagem bolhas.png
    original = cv::imread("bolhas.png",cv::IMREAD_GRAYSCALE);
    filtraBordas(imagem); // Tira as bolhas tocando a borda da imagem
    int qtdBolha=0, qtdBuraco=0;

    imshow("Sem bordas", imagem); // Mostra a imagem sem as bolhas nas bordas
    imwrite("sem_borda.png", imagem);
    for(int y=0; y<imagem.cols; y++){
        for(int x=0; x<imagem.rows; x++){
            if(imagem.at<uchar>(x, y)==255){
                qtdBolha++;
                floodfill(imagem, cv::Point(y, x), qtdBolha*5);
            }
        }
    }

    imshow("Original", original);
    imshow("Bolhas", imagem);
    imwrite("result_Bolhas.png", imagem);
    cout<<"Foram encontradas "<<qtdBolha<<" bolhas, ";

    floodfill(imagem, cv::Point(0,0), 255); /* Realiza a Pintura do fundo da imagem de branco, 
    						com o intuito de contar a quantida de buracos dentro de bohlas
    					     */

    for(int y=0; y<imagem.cols; y++){ // A matriz está sendo percorrida de coluna à coluna a partir da linha 0
        for(int x=0; x<imagem.rows; x++){
            if(imagem.at<uchar>(x, y)==0){ 
                qtdBuraco++;
                floodfill(imagem, cv::Point(y, x), qtdBuraco*10);
            }
        }
    }

    imshow("Buracos", imagem);
    imwrite("buracos.png", imagem);
    cout<<qtdBuraco<<" com buraco."<<endl;
    waitKey();

    return 0;
}

```

![bolhas](https://user-images.githubusercontent.com/37122281/96952604-996b7000-14c5-11eb-811d-8e04beb221c2.png)

**bolhas.png**

![buracos](https://user-images.githubusercontent.com/37122281/96952608-9d978d80-14c5-11eb-9e01-87eec2958f41.png)

**buracos.png**

![result_Bolhas](https://user-images.githubusercontent.com/37122281/96952613-a4260500-14c5-11eb-8ede-3ced0b0194d9.png)

**result_Bolhas.png**

![sem_borda](https://user-images.githubusercontent.com/37122281/96952618-a7b98c00-14c5-11eb-9773-6ffd4c2f8ddd.png)

**sem_borda.png**

### 3.1 Manipulação de Histograma - Equalização

Utilizando o programa **histogram.cpp** como referência, foi implementado um programa chamado **equalize.cpp**. Para cada imagem capturada, ele realiza a equalização do histogram antes da exibição dela. A implementação foi testada apontando a webcam do notebook para ambientes com iluminações variadas e observando o efeito gerado. As imagens processadas estavam em tons de cinza.

**equalize.cpp**
```cpp
#include <opencv2/opencv.hpp>
#include <iostream>
using namespace std;
using namespace cv;

#define CV_RGB2GRAY 6

/*
   Ao Receber uma imagem e a quantidade de barras do histograma desejado
   o programa retorna o histograma
 */
Mat gerar_Hist(Mat const imagem, int bins){
    int hist_Size[] = {bins};
    float lranges[] = {0, 256}; // Vetor de float com os valores lranges[0] = 0.0 e lranges[1] = 256.0
    const float* ranges[] = {lranges}; // Ponteiro para um vetor de float inicializado com o valor ranges[0] = &lranges
    Mat hist;
    int channels[]={0}; // Vetor de inteiros inicializado com channels[0] = 0 e só
    calcHist(&imagem, 1, channels, Mat(), hist, 1, hist_Size, ranges, true, false);
    return hist;
}

/**
 * Recebe um histograma e a quantidade de barras nele
 * Retorna uma imagem do histograma
 */
Mat3b hist_Imagem(Mat const hist, int bins){
    int const hist_height = 256;
    Mat3b hist_imagem = Mat3b::zeros(hist_height, bins); // Matriz na qual cada pixels tem 3 Bytes inicializada com 0 em todas posições
                                               //bins = largura do histograma
    double max_val = 0;
    minMaxLoc(hist, 0, &max_val);

    for(int b=0; b<256; b++){ // Imprimindo cada retângulo no fundo preto
        float const binVal = hist.at<float>(b);
        int const height = cvRound(binVal*hist_height/max_val);
        line(hist_imagem, Point(b, hist_height-height),
         Point(b, hist_height), Scalar::all(255));
    }
    return hist_imagem;
}


int main(){
    Mat imagem; // Imagem capturada pela webcam
    VideoCapture cap; // Objeto capturador

    cap.open(0);
    if(!cap.isOpened())
        return -1;

    while(1){
        cap >> imagem;
        cvtColor(imagem, imagem, CV_RGB2GRAY); // Transforma "imagem" numa imagem em escala de cinza
        Mat result_equalize;
    	equalizeHist(imagem, result_equalize); //aplicando a função de equalizar a imagem

    	imshow("Original", imagem); // Mostra original
    	imshow("Histograma do original", hist_Imagem(gerar_Hist(imagem, 256), 256)); // Cria o vetor histograma e depois a imagem dele

    	imshow("Imagem Equalizada", result_equalize);
    	imshow("Histograma da Equalizada", hist_Imagem(gerar_Hist(result_equalize, 256), 256));
        if(waitKey(30) >= 0){
         	break;
        }
    }
    return 0;
}

```
**Imagem_original.png**

![Imagem_original](https://user-images.githubusercontent.com/37122281/96957828-1e5c8680-14d2-11eb-87c0-59936585e249.png)


**Imagem_equalizada.png**

![Imagem_equalizada](https://user-images.githubusercontent.com/37122281/96957832-21577700-14d2-11eb-802f-85b081292869.png)


**Histograma_original.png**

![Histograma_original](https://user-images.githubusercontent.com/37122281/96957840-25839480-14d2-11eb-875e-36dc81e25fc5.png)


**Histograma_equalizado.png**

![Histograma_equalizado](https://user-images.githubusercontent.com/37122281/96957848-29171b80-14d2-11eb-82ec-518bf5ad03d8.png)

### 3.1 Manipulação de Histograma - Detector de Movimento

Utilizando o programa histogram.cpp como referência, foi realizada a implementação de um programa chamado motiondetector.cpp. Ele calcula continuamente o histograma da imagem (em tons de cinza por causa do CV_RGB2GRAY) e compara com o último histograma calculado. Um movimento é dettectado quando a diferença entre os histogramas presentes e passados é significativa. O método de comparação utilizado na função de comparação foi o CV_COMP_BHATTACHARYYA. Ela retorna um *float* de 0 a 1. 


