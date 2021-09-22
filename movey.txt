#!/usr/bin/env python
import rospy
from geometry_msgs.msg import Twist
PI = 3.14159265359


def move():
        #Comecando o node
        rospy.init_node('tartaruga_reta', anonymous=True)
        velocidade_publisher = rospy.Publisher('/turtle1/cmd_vel', Twist, queue_size=10)
        vel_msg = Twist()

        #Recebendo a entrada do usuario
        print("Vamos mover a tartaruga ")
        velocidade = input("Digite a velocidade ")
        distancia = input("Digite a distancia ")
        cima = input("Para cima? ")#True ou False
        sentido = input("Qual o sentido atual da tartaruga? 1 = eixo x 0 = eixo y ")

        #Virar para eixo x
        if(sentido):
                angulo = 90
        else:
                angulo = 0

        #Calculo velocidade angular
        velocidade_angular = 100*2*PI/360
        angulo_relativo = angulo*2*PI/360
        vel_msg.angular.z = abs(velocidade_angular)
        t0 = rospy.Time.now().to_sec()
        angulo_atual=0

        while(angulo_atual < angulo_relativo):
                velocidade_publisher.publish(vel_msg)
                t1 = rospy.Time.now().to_sec()
                angulo_atual = velocidade_angular*(t1-t0)
        vel_msg.angular.z = 0
        velocidade_publisher.publish(vel_msg)


        #Verifica se o movimento e para frente ou para tras
        if(cima):
                vel_msg.linear.x = abs(velocidade)
        else:
                vel_msg.linear.x = -abs(velocidade)


        #Desde que o movimento seja apenas no eixo x
        vel_msg.linear.y = 0
        vel_msg.linear.z = 0
	vel_msg.angular.x = 0
        vel_msg.angular.y = 0


        while not  rospy.is_shutdown():
        #seta o tempo atual para o calculo da distancia
                t0 = rospy.Time.now().to_sec()
                distancia_atual = 0

                #Loop para mover a tartaruga em uma distancia especifica
                while(distancia_atual<distancia):
                        #Publica a velocidade 
                        velocidade_publisher.publish(vel_msg)
                        #Pega o valor atual para o calculo de velocidade
                        t1=rospy.Time.now().to_sec()
                        #Calcula a distancia da posicao
                        distancia_atual=velocidade*(t1-t0)

                #Depois do loop, para o robo
                vel_msg.linear.x=0
                #Forca o robo a parar
                velocidade_publisher.publish(vel_msg)

if __name__ == '__main__':
        try:
                #testando a funcao
                move()
        except rospy.ROSInterruptException: pass


