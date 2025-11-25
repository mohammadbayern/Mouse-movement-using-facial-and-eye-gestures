# Mouse-movement-using-facial-and-eye-gestures
 Hello every body in this project i want Mouse movement by using facial and eye gestures


# import libraries 
# !/usr/bin/env python3
import cv2
import pyautogui as robot
import time

# load haarcascade models for face and eye detection
eye_model = cv2.CascadeClassifier("haarcascade_eye.xml")
face_model = cv2.CascadeClassifier("haarcascade_frontalface_default.xml")

# main
loop = True
# start video capture from web camera
cam = cv2.VideoCapture(0)

# main  loop
while loop:
   # read frame from camera
   ret , frame = cam.read()
   # flip the frame horizontally
   frame = cv2.flip(frame,1)
   # convert frame to grayscale
   gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
   # detect faces in the grayscale frame
   face = face_model.detectMultiScale(gray)
   # make a copy of the original frame for output
   imgout = frame.copy()
   # if at least one face is detected

   if (len(face)>0):
      x = face[0][0]
      y = face[0][1]
      x2 = x + face[0][2]
      y2 = y + face[0][3]
      # extract the face region from grayscale image
      gray_face = gray[y:y2, x:x2]
      # detect eyes within the face region
      eye = eye_model.detectMultiScale(gray_face, minSize=(30,30), scaleFactor=1.1, minNeighbors=5)
      print('x',x , ' y:',y)

      # draw rectangle around detected face
      cv2.rectangle(imgout, (x, y), (x2, y2), (0, 250, 0), 3)
      # define colors
      white = (250, 250, 250)
      red = (0, 0, 250)
      color = white
      # get current mouse position
      mouse_x = robot.position().x
      mouse_y = robot.position().y

      # check face position and move mouse accordingly
      if x < 205:
         color = red
         mouse_x -= abs(x - 205)
         robot.moveTo(mouse_x, mouse_y, 0)
      # check if right edge of face is beyond threshold
      if x2 > 475:
         color = red
         mouse_x += abs(x2 - 475)
         robot.moveTo(mouse_x, mouse_y, 0)
      # check if top edge of face is above threshold
      if y < 100:
         color = red
         mouse_y -= abs(y - 100)
         robot.moveTo(mouse_x, mouse_y, 0)
      # check if bottom edge of face is below threshold
      if y2 > 350:
         color = red
         mouse_y += abs(y2 - 350)
         robot.moveTo(mouse_x, mouse_y, 0)

      # draw boundary rectangle( here i draw it acording to my face position) but you should adjust it according to your face position in camera
      cv2.rectangle(imgout, (205, 100), (475, 350), color, 2)

      # draw rectangles around detected eyes
      ic = 0
      # loop through detected eyes and draw rectangles
      for (xe, ye, w, h) in eye:
         # increment eye counter
         ic += 1
         # draw rectangle around each eye
         cv2.rectangle(imgout, (xe + x, ye + y), (xe + w + x, ye + h + y), (0, 0, 250), 2)
         # break after drawing rectangles for two eyes
         if ic == 2:
            break

   # display the output frame
   cv2.imshow('nima', imgout)
   # check for 'q' key press to exit loop
   if cv2.waitKey(1) == ord('q'):
      # exit the loop
      loop = False
      break

# And finally release camera and close all OpenCV windows   
cam.release()
cv2.destroyAllWindows()         
