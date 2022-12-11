import wand.image
from tkinter import *
from tkinter.filedialog import *
from tkinter.simpledialog import *
from wand.image import*  #윈도우 구성하기위한 라이브러리 등록

#메뉴들을 함수로 등록했습니다

def displayImage(img, width, height) : 
    global window, canvas, paper, photo, newPhoto, oriX, oriY # 전역변수 호출

    window.geometry(str(width) + "x" + str(height)) # 가로세로 너비 (실제 윈도우 크기로)
    if canvas != None :
        canvas.destroy()
    
    canvas = Canvas(window, width = width, height = height)
    paper = PhotoImage(width = width, height = height)
    canvas.create_image((width/2, height/2), image = paper, state = "normal")

    blob = img.make_blob(format = 'RGB') # 블랍으로 수작업으로 반복문을 통해서 그려냄
    for i in range(0, width) :
        for k in range(0, height) :
            r = blob[(i*3*width) + (k*3) + 0]
            g = blob[(i*3*width) + (k*3) + 1]
            b = blob[(i*3*width) + (k*3) + 2]
            paper.put("#%02x%02x%02x" %(r, g, b), (k,i))
    canvas.pack() 

def funOpen(): #열기 버튼 구현
    global window, canvas, paper, photo, newPhoto, oriX, oriY
    readFp = askopenfilename(parent = window, filetypes = (("그림 파일,", "*.jpg;*.jpeg;*.bmp;*.png;*.tif;*gif"),("모든 파일","*.*"))) 
    # 오픈 하기 위해서 다이얼로그를 띄우고 다양한 포멧 지원
    photo = Image(filename = readFp)
    oriX = photo.width
    oriY = photo.heightnewPhoto = photo.clone()
    newPhoto = photo.clone() # 새로운 뉴 포토라는 변수에 기존의 포토를 복사해놓음
    newX = newPhoto.width
    newY = newPhoto.height
    displayImage(newPhoto, newX, newY) # 아까 만든 함수에 뉴포토,뉴X,뉴Y를 전송시켜줌

def funSave(): # 저장 버튼 구현
    global window, canvas, paper, photo, newPhoto, oriX, oriY
    if newPhoto == None: # 저장 할게 없으면 리턴
        return
    saveFp = asksaveasfile(parent = window, mode = "w", defaultextension = ".jpg", filetypes = (("JPG 파일", "*.jpg;*.jpeg"),("모든 파일","*.*")))
    # 라이트 모드와 jpg로 저장되게끔 만듬
    savePhoto = newPhoto.convert("jpg") # 일단 jpg로 전환 시키고 저장
    savePhoto.save(filename = saveFp.name)

def funExit(): # 종료 버튼 구현
    global window 
    window.destroy()

def funZommInOut(): #확대,축소 버튼 구현 
    global window, canvas, paper, photo, newPhoto, oriX, oriY
    scale = askfloat("확대/축소", "확대/축소 배율 (0.1~4.0)을 입력하세요", minvalue = 0.1, maxvalue = 4.0)
    # 실수를 물어보는 다이얼로그
    newPhoto = photo.clone()
    newPhoto.resize(int(oriX * scale), int(oriY * scale))#기본적인 크기에다가 크기를 곱해서 확대,축소
    newX = newPhoto.width
    newY = newPhoto.height
    displayImage(newPhoto, newX, newY)

def funcFlip(): #상하,좌우 반전 버튼 구현
    global window, canvas, paper, photo, newPhoto, oriX, oriY
    newPhoto = photo.clone()
    newPhoto.filp() 
    newX = newPhoto.width
    newY = newPhoto.height
    displayImage(newPhoto, newX, newY)
    
def funcFlop():
    global window, canvas, paper, photo, newPhoto, oriX, oriY
    newPhoto = photo.clone()
    newPhoto.fiop()
    newX = newPhoto.width
    newY = newPhoto.height
    displayImage(newPhoto, newX, newY)
    global window, canvas, paper, photo, newPhoto, oriX, oriY

def funRotate(): #회전 버튼 구현
    global window, canvas, paper, photo, newPhoto, oriX, oriY
    degree = askinteger("회전", "회전할 각도(0~360)를 입력하세요.", mivalue = 0, maxvalue = 360)
    newPhoto = photo.clone()
    newPhoto.rotate(degree) #각도 입력값에 따라서 회전함
    newX = newPhoto.width
    newY = newPhoto.height
    displayImage(newPhoto, newX, newY)

def funBrightness(): #명도 버튼 구현
    global window, canvas, paper, photo, newPhoto, oriX, oriY
    value = askinteger("명도", "값을 입력하세요(0~200)", mivalue = 0, maxvalue = 200)
    newPhoto = photo.clone() 
    newPhoto.modulate(value, 100, 100) #명도,채도,색도
    newX = newPhoto.width
    newY = newPhoto.height
    displayImage(newPhoto, newX, newY)

def funcSaturation():
    global window, canvas, paper, photo, newPhoto, oriX, oriY
    value  = askinteger("채도", "값을 입력하세요(0~200)", mivalue = 0, maxvalue = 200)
    newPhoto = photo.clone()
    newPhoto.modulate(100, value, 100) #명도,채도,색도
    newX = newPhoto.width
    newY = newPhoto.height
    displayImage(newPhoto, newX, newY)

def funcHue():
    global window, canvas, paper, photo, newPhoto, oriX, oriY
    value  = askinteger("색조", "값을 입력하세요(0~200)", mivalue = 0, maxvalue = 200)
    newPhoto = photo.clone()
    newPhoto.modulate(100, 100, value)
    newX = newPhoto.width
    newY = newPhoto.height
    displayImage(newPhoto, newX, newY)

def funBW(): #흑백으로 바꿔주는 버튼 구현
    global window, canvas, paper, photo, newPhoto, oriX, oriY
    newPhoto = photo.clone()
    newPhoto.type = "grayscale"
    newX = newPhoto.width
    newY = newPhoto.height
    displayImage(newPhoto, newX, newY)


window, canvas, paper = None, None, None #전역 변수 선언
photo, newPhoto = None, None
oriX,oriY = 0,0

# 윈도우 메뉴구성

window = Tk()
window.geometry("400x400") # 윈도우 크기
window.title("파이썬 포토샵") # 윈도우 타이틀

mainMenu = Menu(window) # 메인 메뉴
window.config(menu = mainMenu)
photo = PhotoImage()
pLabel = Label(window, image = photo)
pLabel.pack(expand = 1, anchor = CENTER)

fileMenu = Menu(mainMenu) #파일 메뉴
mainMenu.add_cascade(label = "파일", menu = fileMenu)
fileMenu.add_command(label = "열기", command = funcOpen)
fileMenu.add_command(label = "저장", command = funcSave)
fileMenu.add_separator() # 메뉴구성을 위해서 레이아웃 가르기
fileMenu.add_command(label = "종료", command = funExit)

imageMenu = Menu(mainMenu) #이미지 메뉴
mainMenu.add_cascade(lebel = "이미지", menu = imageMenu)
imageMenu.add_command(label = "확대/축소", command = funcZoomInOut)
imageMenu.add_separator()
imageMenu.add_command(label = "상하 반전", command = funcFlip)
imageMenu.add_command(label = "좌우 반전", command = funcFlop)
imageMenu.add_command(label = "회전", command = funcRotate)
imageMenu.add_separator()
imageMenu.add_command(label = "명도", command = funcBirghtness)
imageMenu.add_command(label = "채도", command = funcSaturation)
imageMenu.add_command(label = "확대/축소", command = funcHue)
imageMenu.add_separator()
imageMenu.add_command(label = "흑백", command = funcBW)
window.mainloop()
