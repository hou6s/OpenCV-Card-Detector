![](Aspose.Words.780fbf9b-f731-414a-a431-edda19f38538.001.jpeg)

**Detecciónde cartascon OpenCV![](Aspose.Words.780fbf9b-f731-414a-a431-edda19f38538.002.png)**

04/09/20XX

**─**

JavierCasasLorenzo JorgeMagallónPolo

VISIÓNPORCOMPUTADORENLAINDUSTRIA MUAII

UPV

11![](Aspose.Words.780fbf9b-f731-414a-a431-edda19f38538.002.png)

Visión general

El objetivo principal de este proyecto es tomar diferentes imágenes de cartas de poker y <a name="_page1_x72.00_y182.25"></a>diseñar un algoritmo para poder clasificarlas dependiendo del tipo de carta.

**Objetivos**

1. Crear un algoritmo de clasificación de cartas mediante OpenCV.
1. Evaluación de resultados.

<a name="_page1_x72.00_y342.75"></a>**Índice**

[**Objetivos 1** ](#_page1_x72.00_y182.25)[Índice 1** ](#_page1_x72.00_y342.75)[Análisis del algoritmo empleado. 2**](#_page2_x72.00_y110.25)

1. [Obtención de las imágenes y detección de los contornos de las cartas. 2 ](#_page2_x72.00_y162.00)[Explicación: 2](#_page2_x72.00_y197.25)
1. [Detección de palo y número en cada carta. 3 ](#_page3_x72.00_y88.50)[Explicación: 3 ](#_page3_x72.00_y123.75)[¿Por qué rotar la carta 180 grados? 4](#_page4_x72.00_y126.00)
1. [Análisis de resultados 5](#_page5_x72.00_y110.25)

[**Código en python: 6**](#_page6_x72.00_y88.50)

1. [Parte 6](#_page6_x72.00_y140.25)
1. [Parte 7](#_page7_x72.00_y88.50)
1. [Funciones auxiliares 10](#_page10_x72.00_y88.50)

<a name="_page2_x72.00_y110.25"></a>**Análisisdelalgoritmoempleado.**

1. Obtencióndelasimágenesydeteccióndeloscontornosdelascartas.

   <a name="_page2_x72.00_y162.00"></a><a name="_page2_x72.00_y197.25"></a>Explicación:

Para empezar debemos encontrar los contornos de las cartas, para ello, se ![](Aspose.Words.780fbf9b-f731-414a-a431-edda19f38538.003.png)procedio a leer las imagenes y aplicar un threshold que nos devolvió una imagen binaria de la original donde el tapete se volvia 0 (negro) y las cartas 1(blanco).

En esta imagen binaria podremos usar el método de cv2.findContours para para encontrar todos los contornos de la imagen.

Por último filtraremos los contornos en función de su área para descartar los más pequeños y quedarnos solo con los contornos de las cartas.

Estos contornos serán anexados a una lista con todos los contornos de cartas en la imagen.

2. Deteccióndepaloynúmeroencadacarta.

<a name="_page3_x72.00_y88.50"></a><a name="_page3_x72.00_y123.75"></a>Explicación:![](Aspose.Words.780fbf9b-f731-414a-a431-edda19f38538.004.png)

Para cada contorno se procede a averiguar cual es el rectángulo de menor área que puede contener ese contorno con cv2.minAreaRect() donde obtendremos los puntos de ese rectángulo.

Ahora tenemos que saber cómo están colocados esos puntos en el espacio para poder saber cuánto girar la carta. Para eso se procedió a la creación de la función auxiliar averiguar\_puntoVert(box).

Dependiendo de si la carta está girada en el sentido de las agujas del reloj o en el sentido contrario a las agujas del reloj, procederemos a girar la carta para que en el eje de las x quede su lado más corto y en el de las y su lado más largo.

Después recortamos la imagen para obtener solo la carta de interés y la hacemos de un tamaño uniforme, en este caso (800,1224).

Queremos que todas las imágenes sean iguales para que al comparar con nuestras máscaras de números y palos obtenidas de imágenes de cartas de ese tamaño obtengamos resultados apropiados.

Ahora para incrementar la robustez del algoritmo giraremos la carta 180 grados ya que los números son simétricos en ambas esquinas y compararemos nuestra máscara solo en la esquina superior izquierda de la carta. Esto se hace para hacer el algoritmo más rápido que si se compara la plantilla con toda la carta.

![](Aspose.Words.780fbf9b-f731-414a-a431-edda19f38538.005.png)

*Imágenes usadas como plantillas del número.*

Con los valores que nos devuelve la función match template localizamos los mínimos de cada template. El mínimo de entre todas será el número y el palo de la carta.

Por último procedemos a indicar en la imagen original el palo y número detectado de cada <a name="_page4_x72.00_y126.00"></a>carta.

¿Porquérotarlacarta180grados?

![](Aspose.Words.780fbf9b-f731-414a-a431-edda19f38538.006.png)

La cámara tiene distorsiones radiales que hará que las cosas parezcan más pequeñas a medida que se alejan de su centro de imagen. eso hará que las cartas no parezcan rectángulos perfectos y por tanto un rectángulo comprenderá un poco más que el perímetro de la carta detectado. Se puede apreciar que al rotar la carta 180 grados las comparaciones con nuestra plantilla darán mejores resultados. Una solucion mas facil seria calibrar intrínsecamente la cámara pero carecemos de datos para ello.

3. Análisisderesultados(Test\_2)

![](Aspose.Words.780fbf9b-f731-414a-a431-edda19f38538.007.jpeg)

<a name="_page5_x72.00_y110.25"></a>**Imagen 13 de Test\_2![](Aspose.Words.780fbf9b-f731-414a-a431-edda19f38538.008.png)**

![](Aspose.Words.780fbf9b-f731-414a-a431-edda19f38538.009.jpeg)

**Imagen 2 de Test\_2**

Se consiguieron 102 aciertos de 104 cartas lo cual da una **precisión de 0.981.** La única imagen en la que hubo fallos fue la dos en la que cabe resaltar que las cartas estaban bastante difuminadas.

<a name="_page6_x72.00_y88.50"></a>**Códigoenpython:**

1. Parte![](Aspose.Words.780fbf9b-f731-414a-a431-edda19f38538.010.png)

<a name="_page6_x72.00_y140.25"></a>for img in glob.glob("Fotos\_Poker\_1/\*.jpg"):

n= cv2.imread(img)

n\_rgb = cv2.cvtColor(n, cv2.COLOR\_BGR2RGB) cv\_img.append(n\_rgb)

for n in range(len(cv\_img)):

img = cv\_img[n]

img\_g = cv2.cvtColor(img, cv2.COLOR\_BGR2GRAY) ret,thresh = cv2.threshold(img\_g,170,255,0)

cnts = cv2.findContours(thresh, cv2.RETR\_EXTERNAL, cv2.CHAIN\_APPROX\_SIMPLE)

cnts = cnts[0] if len(cnts) == 2 else cnts[1] threshold\_min\_area = 12000

number\_of\_contours = 0

card\_cont = []

for c in cnts:

area = cv2.contourArea(c)

if area > threshold\_min\_area:

cv2.drawContours(img, [c], 0, (36,255,12), 3) number\_of\_contours += 1

card\_cont.append(c)

2. Parte![ref1]

<a name="_page7_x72.00_y88.50"></a>for i in range(len(card\_cont)):

#Para todas las cartas que se detectaron contornos hago el menor rectangulo que las abarca.

rot\_rect = cv2.minAreaRect(card\_cont[i])

box = cv2.boxPoints(rot\_rect)

box = box.astype(int)

for p in box:

pt = (p[0],p[1])

#print(pt)

#dibujar rectangulo en las cartas detectadas #rot\_bbox = img.copy() #cv2.drawContours(rot\_bbox,[box],0,(255,0,0),2) #plt.imshow(rot\_bbox)

n,esquina = averiguar\_puntoVert(box)

ang = np.arcsin(abs(box[0][0]-box[n][0])/calc\_dist(box[0],box[n])) #calculo el angulo a corregir de la recta vertical de la carta.

if(box[0][1]<box[n][1]): # dependiendo de como este girada la rotacion sera positiva o negativa para poner la carta recta.

img\_rot,rotation\_mat = rotate\_image(img,-ang\*180/math.pi) else:

img\_rot,rotation\_mat = rotate\_image(img,ang\*180/math.pi)

#conseguimos la matriz de rotacion usada al girar la imagen

rot\_mat = np.array([[rotation\_mat[0][0],rotation\_mat[0][1]],[rotation\_mat[1][0],rotat ion\_mat[1][1]]])

#calculamos los puntos en la nueva imagen anyadiendo la tercera columna que era la de offset

pt\_init = np.dot(rot\_mat,box[0])+rotation\_mat[0:2,2]

pt\_end = np.dot(rot\_mat,box[esquina])+rotation\_mat[0:2,2] #recortamos la imagen para contener solo a la carta.

img\_crop = img\_rot[int(min(pt\_init[1],pt\_end[1])):int(max(pt\_end[1],pt\_init[1])),int(m in(pt\_init[0],pt\_end[0])):int(max(pt\_end[0],pt\_init[0]))]

plt.imshow(img\_crop)

rs=cv2.resize(img\_crop, (800, int(800\*1.53)))#hago la imagen de un tamanyo determinado siempre, para comparar con las templates

rs1 = rs[0:int(400\*1.53),0:400+100] #recorto la imagen a 1/4 de su tamanyo mas un pequenyo margen para comparar si es el 2![](Aspose.Words.780fbf9b-f731-414a-a431-edda19f38538.012.png)

#roto imagen 180 grados por si se ajusta mejor a las esquinas de la carta, lo hace mas robusto.

rs2 = cv2.rotate(rs, cv2.ROTATE\_180)

rs2 = rs2[0:int(400\*1.53),0:400+100]

num\_g1 = cv2.cvtColor(rs1, cv2.COLOR\_BGR2GRAY)

ret,thresh1 = cv2.threshold(num\_g1,170,255,0)

num\_g2 = cv2.cvtColor(rs2, cv2.COLOR\_BGR2GRAY)

ret,thresh2 = cv2.threshold(num\_g2,170,255,0)

#plt.imshow(thresh2)

#plt.show(block=True)

minimo = 1

for im in glob.glob("Ref/\*.png"):

#para cada template de numeros hago match con mi imagen y busco el valor minimo con el metodo de diferencias cuadradas

n= cv2.imread(im)

temp = cv2.cvtColor(n, cv2.COLOR\_BGR2GRAY)

res1 = cv2.matchTemplate(thresh1,temp,cv2.TM\_SQDIFF\_NORMED) min\_val1, max\_val1, min\_loc1, max\_loc1 = cv2.minMaxLoc(res1)

res2 = cv2.matchTemplate(thresh2,temp,cv2.TM\_SQDIFF\_NORMED) min\_val2, max\_val2, min\_loc2, max\_loc2 = cv2.minMaxLoc(res2)

min\_val = min(min\_val1,min\_val2) #cogemos el valor minimo de la comparacion entre el trozo de carta y la plantilla

#print(min\_val)

if min\_val < minimo:

minimo = min\_val

best\_match = temp

best\_match\_name = im

best\_match\_name = best\_match\_name.partition('/')[2] best\_match\_name = best\_match\_name.partition('.')[0] minimo=1

for im in glob.glob("RefPalo/\*.png"):

#igual que antes pero con los palos![](Aspose.Words.780fbf9b-f731-414a-a431-edda19f38538.013.png)

n= cv2.imread(im)

temp = cv2.cvtColor(n, cv2.COLOR\_BGR2GRAY)

res1 = cv2.matchTemplate(thresh1,temp,cv2.TM\_SQDIFF\_NORMED) min\_val1, max\_val1, min\_loc1, max\_loc1 = cv2.minMaxLoc(res1)

res2 = cv2.matchTemplate(thresh2,temp,cv2.TM\_SQDIFF\_NORMED) min\_val2, max\_val2, min\_loc2, max\_loc2 = cv2.minMaxLoc(res2)

min\_val = min(min\_val1,min\_val2)

#print(min\_val)

if min\_val < minimo:

minimo = min\_val

best\_match = temp

best\_match\_name\_palo = im

best\_match\_name\_palo = best\_match\_name\_palo.partition('/')[2] best\_match\_name\_palo = best\_match\_name\_palo.partition('.')[0] best\_match\_name\_palo = best\_match\_name\_palo.replace("2","")

cv2.putText(img\_crop,best\_match\_name+" de "+best\_match\_name\_palo,(150,100),cv2.FONT\_HERSHEY\_SIMPLEX,2,(0,50,50),2, cv2.LINE\_AA )

x,y = find\_lowest\_x\_y(box)

cv2.putText(img,best\_match\_name+" de "+best\_match\_name\_palo,(x,y),cv2.FONT\_HERSHEY\_SIMPLEX,3,(0,0,255),4, cv2.LINE\_AA )

3. Funcionesauxiliares![ref1]

<a name="_page10_x72.00_y88.50"></a>import cv2

import numpy as np

from matplotlib import pyplot as plt import glob

import math

def calc\_dist(p1,p2):

#Calcular distancia de dos puntos 2D temp = (p1-p2)\*\*2

res = math.sqrt(temp[0]+temp[1]) return res

def rotate\_image(array, angle):

#funcion para rotar la imagen sin eliminar trozos de la imagen height, width = array.shape[:2]

image\_center = (width / 2, height / 2)

rotation\_mat = cv2.getRotationMatrix2D(image\_center, angle, 1) mat\_rot = rotation\_mat

radians = math.radians(angle)

sin = math.sin(radians)

cos = math.cos(radians)

bound\_w = int((height \* abs(sin)) + (width \* abs(cos))) bound\_h = int((height \* abs(cos)) + (width \* abs(sin)))

rotation\_mat[0, 2] += ((bound\_w / 2) - image\_center[0]) rotation\_mat[1, 2] += ((bound\_h / 2) - image\_center[1])

rotated\_mat = cv2.warpAffine(array, rotation\_mat, (bound\_w, bound\_h)) return rotated\_mat,rotation\_mat

def averiguar\_puntoVert(box):

#funcion que devuelve el indice del punto que hace la recta vertical de la carta y

#el indice del punto con la recta diagonal que une las dos esquinas mas

lejanas

dist\_min = 9999999

dist\_max = 0

for n in range(1,len(box)):

dist = calc\_dist(box[0],box[n]) if dist > dist\_max:

dist\_max=dist![](Aspose.Words.780fbf9b-f731-414a-a431-edda19f38538.014.png)

esquina = n

if dist < dist\_min:

dist\_min=dist

if(n==1):

indice\_punto = n

if dist > dist\_min and dist < dist\_max:

indice\_punto = n

return indice\_punto,esquina

def find\_lowest\_x\_y(points):

\# Initialize the minimum x and y values with the first point in the

list

min\_x = points[0][0]

max\_y = points[0][1]

for point in points:

if point[0] < min\_x: min\_x = point[0]

if point[1] > max\_y: max\_y = point[1]

return min\_x, max\_y

[ref1]: Aspose.Words.780fbf9b-f731-414a-a431-edda19f38538.011.png
