# DCA0445 - PROCESSAMENTO DIGITAL DE IMAGENS
## 1º Unidade
### 1.1 Manipulando Pixels em uma Imagem

O programa pixels.cpp foi utilizado como referência para o desenvolvimento de um programa chamado regions.cpp. O objetivo desse programa é solicitar ao usuário as coordenadas de dois pontos P1 e P2 localizados dentro dos limites do tamanho da imagem. Os pontos fornecidos formam uma região retangular de vértices opostos e a parcela da imagem comprendida na parte interna a esse retângulo é exibida com o negativo. 

'''cpp

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

'''
