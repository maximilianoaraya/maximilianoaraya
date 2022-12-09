Se suben dos codigos el copia de MAIN es el principal ya que tiene el juego final,
El codigo esta comentado, pero principalmente tiene todos los botones, pero agrege dos la tecla P produce perturbaciones por un tiempo para que el dorn no se salga de
la pantalla, y la tecla M la cual activa el modo manual, despues de aplicar la Tecla A del automatico, sin la tecla A la tecla T no funciona. 


el otro archivo es un .ipynb que se puede abrir en google colab o en jupiter, pero si no puede lo muestro aqui.
import matplotlib.pyplot as plt
import numpy as np
from scipy import signal

def series(sys1, sys2):
    """Series connection of two systems.     """ 
    if not isinstance(sys1, signal.lti):
        sys1 = signal.lti(*sys1)
    if not isinstance(sys2, signal.lti):          
        sys2 = signal.lti(*sys2)
    num = np.polymul(sys1.num, sys2.num)     
    den = np.polymul(sys1.den, sys2.den)     
    sys = signal.lti(num, den) 
    return sys

def feedback(plant, sensor=None):       
    """Negative feedback connection of plant and sensor.     If sensor is None, then it is assumed to be 1.""" 
    if not isinstance(plant, signal.lti):         
        plant = signal.lti(*plant) 
    if sensor is None: 
        sensor = signal.lti([1], [1]) 
    num = np.polymul(plant.num, sensor.den)     
    den = np.polyadd(
        np.polymul(plant.den, sensor.den),                      
        np.polymul(plant.num, sensor.num)
    ) 
    sys = signal.lti(num, den)     
    return sys

K = 10
sys_pc = signal.lti([0, 0.5*K], [1, 5, 0])
sys_prop = feedback(sys_pc)
t = np.linspace(0, 60, num=200)
t, y = signal.step2(sys_prop, T=t)
plt.plot(t, y)
plt.plot([0, t[-1]], [1] * 2, 'k--')
plt.title('Grafico G(s)')
plt.xlabel('tiempo')
plt.ylabel('Output')

////////////////

K_p = 700.0
K_i = 100.0
sys_car = signal.lti([0, 0.5], [1, 5, 0])

sys_pc = series(([K_p, K_i], [1, 0]), sys_car)
sys_prop = feedback(sys_pc)
t = np.linspace(0, 3, num=200)
t, y = signal.step2(sys_prop, T=t)
plt.plot(t, y)
plt.plot([0, t[-1]], [1] * 2, 'k--')


///////////////////
K_p = 500.0
K_i = 100.0
k_d = 10
sys_pc = series(([k_d , K_p, K_i], [1, 0]), sys_car)
sys_prop = feedback(sys_pc)
t = np.linspace(0, 3, num=200)
t, y = signal.step2(sys_prop, T=t)
plt.plot(t, y)
plt.plot([0, t[-1]], [1] * 2, 'k--')
plt.title('Grafico G(s)')
plt.xlabel('tiempo')
plt.ylabel('Output')
