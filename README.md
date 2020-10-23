# DCA0445 - PROCESSAMENTO DIGITAL DE IMAGENS
## 1º Unidade
### 1.1.1 Manipulando Pixels em uma Imagem - Regiões

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

### 1.1.1 Manipulando Pixels em uma Imagem - Troca de quadrantes em Diagonal
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
