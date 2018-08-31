#scriptReaderV3
#Will use threading to both play the record and listen to check if we want
# to quit playing before finishing
from time import sleep
from pynput.mouse import Button, Controller
from pynput.keyboard import Key, Controller
from pynput import keyboard
from pynput import mouse
import ScriptRecorde
import threading
import os

#Setting up control of both mouse and keyboard
mouse = scriptRecorder.Controller()
keyboardControl = Controller()

begunOnce = False

#This will be used to calculate direction moved rather than position.
def termWiseMinus(tuple1,tuple2):
    return tuple(tuple1[i] - tuple2[i] for i in range(len(tuple1)))
        
#Will try to use script recorder file to record a script
#In future versions, would like to just read the log from a file
# as chosen by the user, rather than record a script then play 
try:
    log1 = scriptRecorder.logger()
    log1.record()
except Exception as e:
    print("Likely that nothing was recorded!")
    print e

#The following 3 functions will define how each log is played using pynput
# mouse and keyboard
def mouseClick(timeModifier = 1):
    newTime = 0
    try:
        for i in log1.clickLogs:
            sleep((i[3]-newTime)*timeModifier)
            if i[2] == 'Pressed':
                mouse.press(i[1])
            else:
                mouse.release(i[1])
            newTime = i[3]
    

def keyBoardPress(timeModifier = 1):
    try:
        newTime = 0
        for i in log1.keyLogs:
            sleep((i[1]-newTime)*timeModifier)
            #print(i[0])
            if i[2] == "Pressed":
                keyboardControl.press(i[0])
            else:
                keyboardControl.release(i[0])

            newTime = i[1]


def mouseMov(timeModifier = float(1)):
    try:
        newTime = 0
        try:
            newPosition = log1.mouseLogs[0][0]
            mouse.position = newPosition
            print("PLAYING")
            for i in log1.mouseLogs:
                sleep((i[1]-newTime)*timeModifier)

                new = termWiseMinus(i[0],newPosition)
                mouse.move(*new)

                newPosition = i[0]
                newTime = i[1]
        except IndexError:
            pass

#This function will create a listener that will cancel the whole program
# and stop playing. However, researching this method, it seems to be a
# dangerous way to do it. In future, I'd like to use a queue and conditionals
# in the playing function
def createListener():
    global playAgain
    def on_press(key):
        if key == keyboard.Key.esc:
            os._exit(1)
            return False
    with keyboard.Listener(on_press = on_press) as l:
        l.join()

#The following 4 classes inherit from the threading class to create a
# threads so we can play the seperate components of the recorded inputs
# at once, along with being able to cancel the playing.
class ListenerThread(threading.Thread):
    def __init__(self):
        threading.Thread.__init__(self)
    def run(self):
        createListener()

class mouseMovThread(threading.Thread):
    def __init__(self):
        threading.Thread.__init__(self)
    def run(self):
        mouseMov()

class mouseClickThread(threading.Thread):
    def __init__(self):
        threading.Thread.__init__(self)
    def run(self):
        mouseClick()

class keyboardThread(threading.Thread):
    def run(self):
        keyBoardPress()


#This final try-except clause is just starting all the threads
# and making them run continuously.
#We only really want to run the thread if there is actually SOMETHING to play
#But perhaps with try statements, it wouldn't be much of a slow down to not

try:
    
    lisThr = ListenerThread()
    mouseMovThr = mouseMovThread()
    mouseClickThr = mouseClickThread()
    keyboardThr = keyboardThread()

    lisThr.start()

    while True:
        mouseMovThr = mouseMovThread()
        mouseClickThr = mouseClickThread()
        keyboardThr = keyboardThread()
        
        mouseMovThr.start()
        mouseClickThr.start()
        keyboardThr.start()
        
        mouseMovThr.join()
        mouseClickThr.join()
        keyboardThr.join()

except Exception as e:
   print "Error: unable to start thread"
   print(e)

