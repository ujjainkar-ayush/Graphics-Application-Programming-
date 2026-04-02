# Graphics Application Programming - Practicals 11 to 22

> **Note:** These programs use `graphics.h` (WinBGIm / SDL_bgi). Compile with:
> `gcc program.c -lgraph -lm` (Linux/SDL_bgi) or link BGI lib on Windows.

---

## 11. 2D Translation Transformation

```c
#include <graphics.h>
#include <stdio.h>

int main() {
    int gd = DETECT, gm;
    int x[] = {100, 200, 150};
    int y[] = {100, 100, 50};
    int tx, ty, i, n = 3;

    printf("Enter translation (tx ty): ");
    scanf("%d %d", &tx, &ty);

    initgraph(&gd, &gm, NULL);

    // Original triangle
    setcolor(WHITE);
    outtextxy(10, 10, "Original (White) | Translated (Yellow)");
    for (i = 0; i < n; i++)
        line(x[i], y[i], x[(i+1)%n], y[(i+1)%n]);

    // Translated triangle
    setcolor(YELLOW);
    for (i = 0; i < n; i++)
        line(x[i]+tx, y[i]+ty, x[(i+1)%n]+tx, y[(i+1)%n]+ty);

    getch();
    closegraph();
    return 0;
}
```

---

## 12. 3D Translation Transformation

```c
#include <graphics.h>
#include <stdio.h>

// Simple parallel projection of 3D point to 2D
void project(int x3, int y3, int z3, int *x2, int *y2) {
    *x2 = x3 + z3 / 2;
    *y2 = y3 - z3 / 2;
}

void drawCube(int x, int y, int z, int s, int col) {
    int f[8][2]; // projected vertices
    // 8 corners of cube
    int vx[] = {0,s,s,0,0,s,s,0};
    int vy[] = {0,0,s,s,0,0,s,s};
    int vz[] = {0,0,0,0,s,s,s,s};

    setcolor(col);
    for (int i = 0; i < 8; i++)
        project(x+vx[i], y+vy[i], z+vz[i], &f[i][0], &f[i][1]);

    // front & back faces
    for (int i = 0; i < 4; i++) {
        line(f[i][0], f[i][1], f[(i+1)%4][0], f[(i+1)%4][1]);
        line(f[i+4][0], f[i+4][1], f[(i+1)%4+4][0], f[(i+1)%4+4][1]);
        line(f[i][0], f[i][1], f[i+4][0], f[i+4][1]);
    }
}

int main() {
    int gd = DETECT, gm;
    int tx, ty, tz;

    printf("Enter 3D translation (tx ty tz): ");
    scanf("%d %d %d", &tx, &ty, &tz);

    initgraph(&gd, &gm, NULL);
    outtextxy(10, 10, "White=Original  Yellow=Translated");

    drawCube(200, 200, 50, 80, WHITE);
    drawCube(200+tx, 200+ty, 50+tz, 80, YELLOW);

    getch();
    closegraph();
    return 0;
}
```

---

## 13. Move an Image (Ball) on Screen

```c
#include <graphics.h>
#include <dos.h>

int main() {
    int gd = DETECT, gm;
    initgraph(&gd, &gm, NULL);

    int x = 50, y = 240, mx = getmaxx();

    while (x < mx - 30) {
        setcolor(YELLOW);
        setfillstyle(SOLID_FILL, YELLOW);
        fillellipse(x, y, 20, 20);

        delay(30);

        setcolor(BLACK);
        setfillstyle(SOLID_FILL, BLACK);
        fillellipse(x, y, 20, 20);

        x += 5;
    }

    // draw final position
    setcolor(YELLOW);
    setfillstyle(SOLID_FILL, YELLOW);
    fillellipse(x, y, 20, 20);

    getch();
    closegraph();
    return 0;
}
```

---

## 14. Cubic Bezier Curve

```c
#include <graphics.h>
#include <math.h>

int main() {
    int gd = DETECT, gm;
    // 4 control points
    int cx[] = {100, 150, 350, 400};
    int cy[] = {400, 100, 100, 400};

    initgraph(&gd, &gm, NULL);

    // Draw control polygon
    setcolor(DARKGRAY);
    for (int i = 0; i < 3; i++)
        line(cx[i], cy[i], cx[i+1], cy[i+1]);

    // Mark control points
    setcolor(RED);
    for (int i = 0; i < 4; i++)
        fillellipse(cx[i], cy[i], 4, 4);

    // Draw Bezier curve
    setcolor(GREEN);
    int px = cx[0], py = cy[0];
    for (double t = 0; t <= 1.0; t += 0.001) {
        double u = 1 - t;
        int x = u*u*u*cx[0] + 3*u*u*t*cx[1] + 3*u*t*t*cx[2] + t*t*t*cx[3];
        int y = u*u*u*cy[0] + 3*u*u*t*cy[1] + 3*u*t*t*cy[2] + t*t*t*cy[3];
        line(px, py, x, y);
        px = x; py = y;
    }

    outtextxy(10, 10, "Cubic Bezier Curve");
    getch();
    closegraph();
    return 0;
}
```

---

## 15. Polygon using Absolute and Relative Commands

```c
#include <graphics.h>

int main() {
    int gd = DETECT, gm;
    initgraph(&gd, &gm, NULL);

    // --- Absolute coordinates polygon ---
    setcolor(YELLOW);
    outtextxy(80, 30, "Absolute Polygon");
    int abs_poly[] = {100,100, 200,60, 250,120, 200,200, 100,180, 100,100};
    drawpoly(6, abs_poly);

    // --- Relative coordinates polygon ---
    setcolor(CYAN);
    outtextxy(350, 30, "Relative Polygon");

    // Start point
    int sx = 350, sy = 100;
    // Relative offsets (dx, dy)
    int rel[][2] = {{100,-40},{50,60},{-50,80},{-100,-20},{0,-80}};
    int n = 5;

    int cx = sx, cy = sy;
    for (int i = 0; i < n; i++) {
        int nx = cx + rel[i][0];
        int ny = cy + rel[i][1];
        line(cx, cy, nx, ny);
        cx = nx; cy = ny;
    }
    line(cx, cy, sx, sy); // close polygon

    getch();
    closegraph();
    return 0;
}
```

---

## 16. Clip User-Defined Area of Screen

```c
#include <graphics.h>
#include <stdio.h>

int main() {
    int gd = DETECT, gm;
    int l, t, r, b;

    printf("Enter clip window (left top right bottom): ");
    scanf("%d %d %d %d", &l, &t, &r, &b);

    initgraph(&gd, &gm, NULL);

    // Draw some shapes on full screen
    setcolor(WHITE);
    circle(200, 200, 80);
    rectangle(100, 100, 400, 350);
    line(50, 50, 500, 400);
    outtextxy(10, 10, "Full scene drawn. Press key to clip.");
    getch();

    // Set viewport (clip region)
    setviewport(l, t, r, b, 1); // 1 = clip ON
    clearviewport();

    // Redraw inside clipped area
    setcolor(GREEN);
    rectangle(0, 0, r-l, b-t); // border of clip window
    setcolor(YELLOW);
    circle(200-l, 200-t, 80);
    rectangle(100-l, 100-t, 400-l, 350-t);
    line(50-l, 50-t, 500-l, 400-t);

    outtextxy(5, 5, "Clipped");
    getch();
    closegraph();
    return 0;
}
```

---

## 17. Line Clipping (Cohen-Sutherland)

```c
#include <graphics.h>
#include <stdio.h>

int xmin=150, ymin=100, xmax=400, ymax=350;

enum { LEFT=1, RIGHT=2, BOTTOM=4, TOP=8 };

int code(int x, int y) {
    int c = 0;
    if (x < xmin) c |= LEFT;
    if (x > xmax) c |= RIGHT;
    if (y > ymax) c |= BOTTOM;
    if (y < ymin) c |= TOP;
    return c;
}

void cohenSutherland(int x1, int y1, int x2, int y2) {
    int c1 = code(x1,y1), c2 = code(x2,y2);
    int accept = 0;

    while (1) {
        if (!(c1|c2)) { accept=1; break; }
        if (c1&c2) break;

        int c = c1 ? c1 : c2;
        int x, y;
        if (c & TOP)        { x=x1+(x2-x1)*(ymin-y1)/(y2-y1); y=ymin; }
        else if (c & BOTTOM){ x=x1+(x2-x1)*(ymax-y1)/(y2-y1); y=ymax; }
        else if (c & RIGHT) { y=y1+(y2-y1)*(xmax-x1)/(x2-x1); x=xmax; }
        else                { y=y1+(y2-y1)*(xmin-x1)/(x2-x1); x=xmin; }

        if (c == c1) { x1=x; y1=y; c1=code(x,y); }
        else         { x2=x; y2=y; c2=code(x,y); }
    }

    if (accept) {
        setcolor(GREEN);
        line(x1, y1, x2, y2);
    }
}

int main() {
    int gd=DETECT, gm;
    int x1, y1, x2, y2;

    printf("Enter line (x1 y1 x2 y2): ");
    scanf("%d %d %d %d", &x1, &y1, &x2, &y2);

    initgraph(&gd, &gm, NULL);

    setcolor(WHITE);
    rectangle(xmin, ymin, xmax, ymax);
    outtextxy(10,10,"White=Window  Red=Original  Green=Clipped");

    setcolor(RED);
    line(x1, y1, x2, y2);

    cohenSutherland(x1, y1, x2, y2);

    getch();
    closegraph();
    return 0;
}
```

---

## 18. Polygon Clipping (Sutherland-Hodgman)

```c
#include <graphics.h>
#include <stdio.h>

int xmin=150, ymin=100, xmax=450, ymax=350;

typedef struct { double x, y; } Pt;

int clipEdge(Pt *in, int n, Pt *out, int edge) {
    int cnt = 0;
    for (int i = 0; i < n; i++) {
        Pt c = in[i], p = in[(i+n-1)%n];
        double ci, pi;
        // inside test per edge
        switch(edge) {
            case 0: ci=c.x-xmin; pi=p.x-xmin; break; // left
            case 1: ci=xmax-c.x; pi=xmax-p.x; break; // right
            case 2: ci=c.y-ymin; pi=p.y-ymin; break; // top
            case 3: ci=ymax-c.y; pi=ymax-p.y; break; // bottom
        }
        if (ci>=0 && pi>=0) { out[cnt++]=c; }
        else if (ci>=0) {
            double t = ci/(ci-pi);
            out[cnt++] = (Pt){c.x+t*(p.x-c.x), c.y+t*(p.y-c.y)};
            out[cnt++] = c;
        } else if (pi>=0) {
            double t = ci/(ci-pi);
            out[cnt++] = (Pt){c.x+t*(p.x-c.x), c.y+t*(p.y-c.y)};
        }
    }
    return cnt;
}

int main() {
    int gd=DETECT, gm;
    initgraph(&gd, &gm, NULL);

    Pt poly[] = {{100,200},{300,50},{500,200},{350,400},{150,400}};
    int n = 5;

    // Draw original
    setcolor(RED);
    for (int i = 0; i < n; i++)
        line(poly[i].x, poly[i].y, poly[(i+1)%n].x, poly[(i+1)%n].y);

    setcolor(WHITE);
    rectangle(xmin, ymin, xmax, ymax);

    // Clip against 4 edges
    Pt buf1[20], buf2[20];
    for (int i=0;i<n;i++) buf1[i]=poly[i];
    int cn = n;
    for (int e = 0; e < 4; e++) {
        cn = clipEdge(buf1, cn, buf2, e);
        for (int i=0;i<cn;i++) buf1[i]=buf2[i];
    }

    // Draw clipped polygon
    setcolor(GREEN);
    for (int i = 0; i < cn; i++)
        line(buf1[i].x, buf1[i].y, buf1[(i+1)%cn].x, buf1[(i+1)%cn].y);

    outtextxy(10,10,"Red=Original  Green=Clipped");
    getch();
    closegraph();
    return 0;
}
```

---

## 19. Rotation of a Point

```c
#include <graphics.h>
#include <math.h>
#include <stdio.h>

int main() {
    int gd=DETECT, gm;
    int px, py, cx, cy;
    float angle;

    printf("Enter point (px py): ");      scanf("%d %d", &px, &py);
    printf("Enter pivot (cx cy): ");      scanf("%d %d", &cx, &cy);
    printf("Enter angle (degrees): ");    scanf("%f", &angle);

    float rad = angle * M_PI / 180.0;
    int nx = cx + (px-cx)*cos(rad) - (py-cy)*sin(rad);
    int ny = cy + (px-cx)*sin(rad) + (py-cy)*cos(rad);

    initgraph(&gd, &gm, NULL);

    // Pivot
    setcolor(WHITE);
    fillellipse(cx, cy, 4, 4);
    outtextxy(cx+8, cy, "Pivot");

    // Original point
    setcolor(RED);
    fillellipse(px, py, 5, 5);
    outtextxy(px+8, py, "Original");

    // Rotated point
    setcolor(GREEN);
    fillellipse(nx, ny, 5, 5);
    outtextxy(nx+8, ny, "Rotated");

    // Show rotation arc
    setcolor(DARKGRAY);
    int r = (int)sqrt((px-cx)*(px-cx)+(py-cy)*(py-cy));
    circle(cx, cy, r);

    getch();
    closegraph();
    return 0;
}
```

---

## 20. Fill Area by Given Pattern

```c
#include <graphics.h>

int main() {
    int gd=DETECT, gm;
    initgraph(&gd, &gm, NULL);

    // User-defined 8x8 pattern
    char pattern[] = {0xAA,0x55,0xAA,0x55,0xAA,0x55,0xAA,0x55};

    // Draw and fill rectangle with pattern
    setcolor(WHITE);
    rectangle(100, 100, 300, 300);
    setfillpattern(pattern, YELLOW);
    floodfill(200, 200, WHITE);

    // Another shape with built-in pattern
    setcolor(WHITE);
    circle(450, 200, 80);
    setfillstyle(HATCH_FILL, CYAN);
    floodfill(450, 200, WHITE);

    outtextxy(120, 80, "Custom Pattern");
    outtextxy(400, 80, "Built-in Pattern");

    getch();
    closegraph();
    return 0;
}
```

---

## 21. Flood Fill Algorithm

```c
#include <graphics.h>

void floodFill(int x, int y, int oldCol, int newCol) {
    if (x<0 || y<0 || x>=getmaxx() || y>=getmaxy()) return;
    if (getpixel(x,y) != oldCol) return;

    putpixel(x, y, newCol);
    floodFill(x+1, y, oldCol, newCol);
    floodFill(x-1, y, oldCol, newCol);
    floodFill(x, y+1, oldCol, newCol);
    floodFill(x, y-1, oldCol, newCol);
}

int main() {
    int gd=DETECT, gm;
    initgraph(&gd, &gm, NULL);

    // Draw a closed boundary
    setcolor(WHITE);
    rectangle(200, 150, 400, 350);

    // Seed point inside
    int sx=300, sy=250;
    int oldColor = getpixel(sx, sy); // BLACK (background)

    outtextxy(10,10, "Flood Fill Demo - filling rectangle...");

    floodFill(sx, sy, oldColor, GREEN);

    getch();
    closegraph();
    return 0;
}
```

> **Tip:** For large areas increase stack size or use an iterative (queue-based) version.

---

## 22. Scan Line Fill Algorithm

```c
#include <graphics.h>
#include <stdio.h>

int main() {
    int gd=DETECT, gm;
    initgraph(&gd, &gm, NULL);

    // Polygon vertices
    int px[] = {200, 350, 400, 300, 150};
    int py[] = {100, 100, 250, 350, 250};
    int n = 5;

    // Draw polygon
    setcolor(WHITE);
    for (int i = 0; i < n; i++)
        line(px[i], py[i], px[(i+1)%n], py[(i+1)%n]);

    // Find ymin, ymax
    int ymin=py[0], ymax=py[0];
    for (int i=1;i<n;i++) {
        if (py[i]<ymin) ymin=py[i];
        if (py[i]>ymax) ymax=py[i];
    }

    // Scan line fill
    for (int y = ymin; y <= ymax; y++) {
        int xi[20], cnt=0;

        for (int i = 0; i < n; i++) {
            int j = (i+1) % n;
            int y1=py[i], y2=py[j], x1=px[i], x2=px[j];
            if ((y1<=y && y2>y) || (y2<=y && y1>y)) {
                xi[cnt++] = x1 + (double)(y-y1)*(x2-x1)/(y2-y1);
            }
        }

        // Sort intersections
        for (int i=0;i<cnt-1;i++)
            for (int j=0;j<cnt-i-1;j++)
                if (xi[j]>xi[j+1]) { int t=xi[j]; xi[j]=xi[j+1]; xi[j+1]=t; }

        // Fill between pairs
        setcolor(CYAN);
        for (int i=0;i<cnt;i+=2)
            line(xi[i], y, xi[i+1], y);
    }

    outtextxy(10, 10, "Scan Line Fill");
    getch();
    closegraph();
    return 0;
}
```

---

## Quick Reference Table

| # | Topic | Key Function / Algorithm |
|---|-------|--------------------------|
| 11 | 2D Translation | `x' = x + tx, y' = y + ty` |
| 12 | 3D Translation | Parallel projection + offset |
| 13 | Ball Animation | `cleardevice` + redraw loop |
| 14 | Bezier Curve | Cubic Bernstein polynomials |
| 15 | Polygon Drawing | `drawpoly()` / relative offsets |
| 16 | Screen Clipping | `setviewport()` |
| 17 | Line Clipping | Cohen-Sutherland |
| 18 | Polygon Clipping | Sutherland-Hodgman |
| 19 | Point Rotation | Rotation matrix formula |
| 20 | Pattern Fill | `setfillpattern()` |
| 21 | Flood Fill | 4-connected recursive fill |
| 22 | Scan Line Fill | Edge-intersection sorting |
