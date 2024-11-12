#include <GL/glut.h>
#include <cmath>
#include <vector>

struct Shape {
    enum Type { RECTANGLE, CIRCLE } type;
    float x, y;
    float size;
};

std::vector<Shape> shapes;
Shape::Type currentShape = Shape::RECTANGLE;

void init() {
    glClearColor(1.0, 1.0, 1.0, 1.0);  // Set background color to white
    glColor3f(0.0, 0.0, 0.0);           // Set drawing color to black
    glPointSize(5.0);
    glMatrixMode(GL_PROJECTION);
    gluOrtho2D(0.0, 800.0, 600.0, 0.0);
}

void drawRectangle(float x, float y, float size) {
    glBegin(GL_QUADS);
    glVertex2f(x - size, y - size);
    glVertex2f(x + size, y - size);
    glVertex2f(x + size, y + size);
    glVertex2f(x - size, y + size);
    glEnd();
}

void drawCircle(float x, float y, float radius) {
    glBegin(GL_POLYGON);
    for (int i = 0; i < 100; i++) {
        float theta = (2.0f * 3.14159f * float(i)) / float(100);  // angle in radians
        float dx = radius * cosf(theta);  // x coordinate
        float dy = radius * sinf(theta);  // y coordinate
        glVertex2f(x + dx, y + dy);
    }
    glEnd();
}

void display() {
    glClear(GL_COLOR_BUFFER_BIT);
    
    for (const auto &shape : shapes) {
        if (shape.type == Shape::RECTANGLE) {
            drawRectangle(shape.x, shape.y, shape.size);
        } else if (shape.type == Shape::CIRCLE) {
            drawCircle(shape.x, shape.y, shape.size);
        }
    }

    glFlush();
}

void mouse(int button, int state, int x, int y) {
    if (button == GLUT_LEFT_BUTTON && state == GLUT_DOWN) {
        Shape newShape;
        newShape.type = currentShape;
        newShape.x = x;
        newShape.y = y;
        newShape.size = 30;  // Default size for shapes
        shapes.push_back(newShape);
        glutPostRedisplay();
    } else if (button == GLUT_RIGHT_BUTTON && state == GLUT_DOWN) {
        shapes.clear();  // Clear all shapes on right-click
        glutPostRedisplay();
    }
}

void keyboard(unsigned char key, int x, int y) {
    if (key == 'r' || key == 'R') {
        currentShape = Shape::RECTANGLE;
    } else if (key == 'c' || key == 'C') {
        currentShape = Shape::CIRCLE;
    }
}

int main(int argc, char** argv) {
    glutInit(&argc, argv);
    glutInitDisplayMode(GLUT_SINGLE | GLUT_RGB);
    glutInitWindowSize(800, 600);
    glutCreateWindow("2D Shape Drawing Tool");

    init();
    glutDisplayFunc(display);
    glutMouseFunc(mouse);
    glutKeyboardFunc(keyboard);

    glutMainLoop();
    return 0;
}
