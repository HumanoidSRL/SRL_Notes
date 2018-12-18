### myopenglwidget.h:
```
#ifndef MYOPENGLWIDGET_H
#define MYOPENGLWIDGET_H
#include<QOpenGLWidget>
#include<QOpenGLBuffer>
class QOpenGLShaderProgram;

class MyOpenGLWidget:public QOpenGLWidget
{
    Q_OBJECT
public:
    explicit MyOpenGLWidget(QWidget *paren=0);
    void keyPressEvent(QKeyEvent*event);
protected:
    void initializeGL();
    void paintGL();
    void resizeGL(int width,int height);
private:
    void paintRobet();
    void paintShoulder();
    void paintJoint();
    void paintLeftMachineArm();
    void paintRightMachineArm();
    void paintLeftMachineLeg();
    void paintRightMachineLeg();

    void setDrawing(GLdouble *,GLdouble*,double,double);
    void reset();

    double translatorx,translatorz;
    double xRot,yRot,zRot;
    double upThetas[6];
    double downThetas[4];
    double elongLeftLeg,elongRightLeg;
    double lengthBody,widthBody,depthBody;
    double heightShoulder,radiusShoulder,radiusJoint,heightJoint;
    double lengthArm,widthArm,thickArm;
    double lengthForearm,widthFoream,thickForearm;

};
#endif // MYOPENGLWIDGET_H
```

### main.cpp:  
```
#include <QApplication>
#include"myopenglwidget.h"
int main(int argc, char *argv[])
{
    QApplication app(argc, argv);
    MyOpenGLWidget w;
    w.resize(1000,1000);
    w.show();

    return app.exec();
}  
```

### myopenglwidget.cpp:
```
#include "myopenglwidget.h"
#include<QKeyEvent>
#include<gl/glut.h>
#include<gl/glu.h>
#include<gl/gl.h>
#include <QDebug>
#include <math.h>


MyOpenGLWidget::MyOpenGLWidget(QWidget *paren)
    :QOpenGLWidget(paren)
{
    translatorx=0;
    translatorz=0;

    xRot=0;
    yRot=0;
    zRot=0;

    for(int i=0;i<6;i++)
        upThetas[i]=0;
    for(int i=0;i<4;i++)
        downThetas[i]=0;
    elongLeftLeg=4;
    elongRightLeg=4;

    lengthBody=8;
    widthBody=6;
    depthBody=3;

    heightShoulder=0.5;
    radiusShoulder=1;
    heightJoint=1.6;
    radiusJoint=0.3;

    lengthArm=6;
    widthArm=heightJoint;
    thickArm=0.5;

    lengthForearm=5.5;
    widthFoream=heightJoint;
    thickForearm=0.5;
}

void MyOpenGLWidget::initializeGL()
{
    GLfloat mat_specular[] = {1.0,1.0,1.0,1.0};   //光源则为白光
    GLfloat mat_shiniess[] = {50.0};
    GLfloat light_position[] = {1.0,1.0,1.0,0.0};
    GLfloat white_light[] = {1.0,1.0,1.0,1.0};
    GLfloat lmodel_ambient[] = {0.1,0.1,0.1,1.0};
    glClearColor(0.0,0.0,0.0,0.0);            //设置背景色 既然不透明度为0 啥也看不见
    glShadeModel(GL_SMOOTH);                  //一说设置两点间的色彩过渡模式，GL_SMOOTH是默认的 过渡色；
    glMaterialfv(GL_FRONT,GL_SPECULAR,mat_specular); //反射光值 用来表现物体表面颜色 第一个参数表示正面受照 第二个表示对平明/镜面光进性设置，第三个参数表示反光率的RGBA值，用来和光源的RGBA值相乘得到进入眼中的光
    glMaterialfv(GL_FRONT,GL_SHININESS,mat_shiniess);//设置反射系数；指定的材料的RGBA反射指数。整数值直接映射。仅接受[0,128]范围内的值。前向和后向材质的默认镜面反射指数为0。
    glLightfv(GL_LIGHT0,GL_POSITION,light_position); //设置第一个光源位置   这里应该是以平行光源形式出现 在无穷远处 不考虑衰减  前三个参数表示位置 （如果是平行光源 表示从那个位置到原点的方向）后一个参数0表示平行  1表示近点光源
    glLightfv(GL_LIGHT0,GL_DIFFUSE,white_light);     //设置光源的散射光属性 这里是强白光
    glLightfv(GL_LIGHT0,GL_SPECULAR,white_light);     //光源的平行光属性为强白光
    glLightModelfv(GL_LIGHT_MODEL_AMBIENT,lmodel_ambient);//设置整个场景的RGBA强度  设置应为全不透明，然后非白光 大概是很淡 比如灰色的

    glEnable(GL_LIGHTING);              //启用灯源
    glEnable(GL_LIGHT0);                //启用灯源0
    glEnable(GL_DEPTH_TEST);            //启用深度测试。根据坐标的远近自动隐藏被遮住的图形（材料） 感觉就是很正常的样子
}


void MyOpenGLWidget::resizeGL(int w, int h)
{
    glViewport(0, 0, (GLint)w, (GLint)h);//glViewport是OpenGL中的一个函数。计算机图形学中，在屏幕上打开窗口的任务是由窗口系统，而不是OpenGL负责的。glViewport在默认情况下，视口被设置为占据打开窗口的整个像素矩形，窗口大小和设置视口大小相同，所以为了选择一个更小的绘图区域，就可以用glViewport函数来实现这一变换，在窗口中定义一个像素矩形，最终将图像映射到这个矩形中。例如可以对窗口区域进行划分，在同一个窗口中显示分割屏幕的效果，以显示多个视图/glViewport(GLint x,GLint y,GLsizei width,GLsizei height)为其函数原型。X，Y————以像素为单位，指定了视口的左下角（在第一象限内，以（0，0）为原点的）位置。width，height————表示这个视口矩形的宽度和高度，根据窗口的实时变化重绘窗口。
    glMatrixMode(GL_PROJECTION);//对接下来一步要做什么进行声明 ：对什么矩阵进行修改  ；例如这里是改变投影
    glLoadIdentity();        //将投影阵改为单位阵
    gluPerspective(60.0, (GLfloat)w/(GLfloat)h, 0.1, 100.0);//设定能看到什么范围的物体；第一个参数表示眼睛睁开的幅度，并且睁开越大感觉物体越远，越小感觉物体越近；第二个参数表示裁剪面的宽长比；可以想见根据距离和睁开幅度可以获得高度，加上宽长比，就都在了；后两个分别表示近剪裁面和远剪裁面到观察点的距离；
    glMatrixMode(GL_MODELVIEW);//切换为模型视图矩阵 继续正确作图
    glLoadIdentity();//同样变为单位阵；
}


void MyOpenGLWidget::paintGL()
{
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    glLoadIdentity();
    gluLookAt(0,0,50,GLfloat(2)/3,GLfloat(2)/3,GLfloat(1)/3,0,1,0);//前三个设置摄像机在世界坐标中的位置；中间三个设置所看向的物体在世界坐标中的位置；最后三个确定摄像机的头顶外法向的方向；
    paintRobet();
    glFlush();
}

void MyOpenGLWidget::paintRobet()
{
    paintLeftMachineArm();
    paintRightMachineArm();
    paintLeftMachineLeg();
    paintRightMachineLeg();
}

void MyOpenGLWidget::paintLeftMachineArm()
{
    glTranslated(0,0,translatorz);
    glRotatef(yRot,0,1,0);
    glRotatef(xRot,1,0,0);
    glRotatef(zRot,0,0,1);

    double flex[3]={widthBody/depthBody,lengthBody/depthBody,1};
    glScalef(flex[0],flex[1],flex[2]);
    glutSolidCube(depthBody);
    glScalef(1/flex[0],1/flex[1],1/flex[2]);

    glTranslated(0,lengthBody/2-radiusShoulder,0);//向上移动半个身子长度减去肩部半径的长度 确定了肩的高度
    glTranslated(-widthBody/2,0,0);                //原点移动到观察者左侧的肩部
    glRotatef(-90,0,1,0);                          //让z轴朝向肩关节处它的外法向；
    paintShoulder();                               //画肩部

    glTranslated(0,0,thickArm/2+heightShoulder);   //原点移动到肩部外侧+厚度一半；
    glRotatef(upThetas[2],0,0,1);                       //贴着身子旋转theta1角度，向上为正,原始形态为垂直向下
    glRotatef(-upThetas[1],1,0,0);                       //向外展开一个角度，以向外展开的角度为正，即给如theta2为正，则向外转出
    glTranslated(0,-lengthArm/2,0);                 //将原点移动到将要绘制的长方体中心
    flex[0]=widthArm/thickArm;
    flex[1]=lengthArm/thickArm;
    flex[2]=1;                                      //初始化之后只能单独赋值
    glScalef(flex[0],flex[1],flex[2]);
    glutSolidCube(thickArm);
    glScalef(1/flex[0],1/flex[1],1/flex[2]);         //绘制上臂

    glTranslated(0,-lengthArm/2-radiusJoint,0);        //原点在将要绘制的joint轴线上
    glTranslated(-widthArm/2,0,0);                     //原点已经在Joint的底面中心了
    glRotatef(90,0,1,0);                                //绕y轴旋转90度，使得z轴正向朝着joint将要被绘制的方向
    paintJoint();

    glTranslated(0,0,heightJoint/2);
    glRotatef(upThetas[0],0,0,1);
    glTranslated(0,-lengthForearm/2-radiusJoint,0);      //使原点到达小臂的长方体中心
    flex[0]=1;
    flex[1]=lengthForearm/thickForearm;
    flex[2]=widthFoream/thickForearm;
    glScalef(flex[0],flex[1],flex[2]);
    glutSolidCube(thickForearm);
    glScalef(1/flex[0],1/flex[1],1/flex[2]);              //绘制小臂
}

void MyOpenGLWidget::paintRightMachineArm()
{
    glLoadIdentity();                                                       //将模型视图矩阵归到原点去（不然太麻烦了）
    gluLookAt(0,0,50,GLfloat(2)/3,GLfloat(2)/3,GLfloat(1)/3,0,1,0);         //同样需要设置观看角度，不然糊里糊涂；

    glTranslated(0,0,translatorz);
    glRotatef(yRot,0,1,0);
    glRotatef(xRot,1,0,0);
    glRotatef(zRot,0,0,1);                                                   //同样的，需要如此才能够变换这些时对它们总体变换；

    glTranslated(0,lengthBody/2-radiusShoulder,0);//向上移动半个身子长度减去肩部半径的长度 确定了肩的高度
    glTranslated(widthBody/2,0,0);                //原点移动到观察者左侧的肩部
    glRotatef(90,0,1,0);                          //让z轴朝向肩关节处它的外法向；
    paintShoulder();                               //画肩部

    glTranslated(0,0,thickArm/2+heightShoulder);   //原点移动到肩部外侧+厚度一半；
    glRotatef(-upThetas[3],0,0,1);                       //贴着身子旋转theta1角度，向上为正,原始形态为垂直向下
    glRotatef(-upThetas[4],1,0,0);                       //向外展开一个角度，以向外展开的角度为正，即给如theta2为正，则向外转出
    glTranslated(0,-lengthArm/2,0);                 //将原点移动到将要绘制的长方体中心
    double flex1[3];
    flex1[0]=widthArm/thickArm;
    flex1[1]=lengthArm/thickArm;
    flex1[2]=1;                                      //初始化之后只能单独赋值
    glScalef(flex1[0],flex1[1],flex1[2]);
    glutSolidCube(thickArm);
    glScalef(1/flex1[0],1/flex1[1],1/flex1[2]);         //绘制上臂

    glTranslated(0,-lengthArm/2-radiusJoint,0);        //原点在将要绘制的joint轴线上
    glTranslated(-widthArm/2,0,0);                     //原点已经在Joint的底面中心了
    glRotatef(90,0,1,0);                                //绕y轴旋转90度，使得z轴正向朝着joint将要被绘制的方向
    paintJoint();

    glTranslated(0,0,heightJoint/2);
    glRotatef(upThetas[5],0,0,1);
    glTranslated(0,-lengthForearm/2-radiusJoint,0);      //使原点到达小臂的长方体中心
    flex1[0]=1;
    flex1[1]=lengthForearm/thickForearm;
    flex1[2]=widthFoream/thickForearm;
    glScalef(flex1[0],flex1[1],flex1[2]);
    glutSolidCube(thickForearm);
    glScalef(1/flex1[0],1/flex1[1],1/flex1[2]);              //绘制小臂
}

void MyOpenGLWidget::paintLeftMachineLeg()
{

}

void MyOpenGLWidget::paintRightMachineLeg()
{

}

void MyOpenGLWidget::paintShoulder()
{
    GLUquadricObj *quadratic;//沿z轴正向画空心圆柱
    quadratic=gluNewQuadric();
    gluCylinder(quadratic,radiusShoulder,radiusShoulder,heightShoulder,32,32);//画圆柱
    gluDeleteQuadric(quadratic);//释放内存
}

void MyOpenGLWidget::paintJoint()
{
    GLUquadricObj *quadratic;//沿z轴正向画空心圆柱
    quadratic=gluNewQuadric();
    gluCylinder(quadratic,radiusJoint,radiusJoint,heightJoint,32,32);//画圆柱
    gluDeleteQuadric(quadratic);//释放内存
}

void MyOpenGLWidget::keyPressEvent(QKeyEvent *event)
{
    switch(event->key()){
    case Qt::Key_Up:
        upThetas[2]+=10;
        break;
    case Qt::Key_Left:
        upThetas[1]+=10;
        break;
    case Qt::Key_Right:
        upThetas[0]+=10;
        break;
    case Qt::Key_W:
        upThetas[3]+=10;
        break;
     case Qt::Key_A:
        upThetas[4]+=10;
        break;
    case Qt::Key_D:
        upThetas[5]+=10;
        break;
    case Qt::Key_1:
        translatorz +=1;
        break;
    case Qt::Key_2:
        translatorz -=1;
        break;
    case Qt::Key_3:
        xRot+=5;
        break;
    case Qt::Key_4:
        yRot+=5;
        break;
    case Qt::Key_5:
        zRot+=5;
        break;
    default:
        break;
    }
    update();
    QOpenGLWidget::keyPressEvent(event);
}
void MyOpenGLWidget::setDrawing(GLdouble upRotate[6],GLdouble downRotate[4],double leftLeg,double rightLeg)
{
    for(int i=0;i<6;i++)
        upThetas[i]=upRotate[i];
    for(int i=0;i<6;i++)
        downThetas[i]=downRotate[i];
    elongLeftLeg=leftLeg;
    elongRightLeg=rightLeg;
    update();//会调用paintGL函数
}

void MyOpenGLWidget::reset()
{
    for(int i=0;i<6;i++)
        upThetas[i]=0;
    for(int i=0;i<4;i++)
        downThetas[i]=0;
    elongLeftLeg=4;
    elongRightLeg=4;
    update();
}
```

