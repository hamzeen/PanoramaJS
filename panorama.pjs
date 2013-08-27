/* @pjs preload="interior.jpg"; */
/**
 * Panorama JS an experimental sketch made with processing.js using 
 * P3D renderer. Photo credits: Photospheres and 360/180 panoramas, 
 * https://plus.google.com/communities/109746796402649001884
 * 
 * @author: Hamzeen. H.
 * @created: 27 August, 2013
 */
PImage sphere_tex;

float rotationY = 0; float velocityY = 0;
float deltax = 0.01;

int sDetail = 90;  // Sphere detail setting
float pushBack = 0; // z coordinate of the center of the sphere
float factor = 0.99; // magnification factor

float[] sphereX, sphereY, sphereZ;
float sinLUT[];
float cosLUT[];
float SINCOS_PRECISION = 0.5;
int SINCOS_LENGTH = int(360.0 / SINCOS_PRECISION);

void setup(){
  sphere_tex = loadImage("interior.jpg");
  size(sphere_tex.width,sphere_tex.height,P3D);
  pushBack = height/20;
  initializeSphere(sDetail);
}

void draw() {
  background(0);
  translate(width / 2.0, height / 2.0, pushBack);
  rotateY(rotationY);
  rotateX(-0.06);

  textureMode(IMAGE);
  noStroke();
  texturedSphere(height*8, sphere_tex);
  
  rotationY += velocityY;
  velocityY *= 0.90;
  if(mousePressed) {
    velocityY -= (mouseX-pmouseX) * 0.005;
  }
}

void keyPressed() {
  if (key == CODED) {
    if (keyCode == RIGHT) {
      velocityY += 0.03;
    } else if (keyCode == LEFT) {
      velocityY -= 0.03;
    }
  }
}

void initializeSphere(int res) {
  sinLUT = new float[SINCOS_LENGTH];
  cosLUT = new float[SINCOS_LENGTH];

  for (int i = 0; i < SINCOS_LENGTH; i++) {
    sinLUT[i] = (float) Math.sin(i * DEG_TO_RAD * SINCOS_PRECISION);
    cosLUT[i] = (float) Math.cos(i * DEG_TO_RAD * SINCOS_PRECISION);
  }

  float delta = (float)SINCOS_LENGTH/res;
  float[] cx = new float[res];
  float[] cz = new float[res];
  
  // Calc unit circle in XZ plane
  for (int i = 0; i < res; i++) {
    cx[i] = -cosLUT[(int) (i*delta) % SINCOS_LENGTH];
    cz[i] = sinLUT[(int) (i*delta) % SINCOS_LENGTH];
  }
  
  // Computing vertexlist vertexlist starts at south pole
  int vertCount = res * (res-1) + 2;
  int currVert = 0;
  
  // Re-init arrays to store vertices
  sphereX = new float[vertCount];
  sphereY = new float[vertCount];
  sphereZ = new float[vertCount];
  float angle_step = (SINCOS_LENGTH*0.5f)/res;
  float angle = angle_step;
  
  // Step along Y axis
  for (int i = 1; i < res; i++) {
    float curradius = sinLUT[(int) angle % SINCOS_LENGTH];
    float currY = -cosLUT[(int) angle % SINCOS_LENGTH];
    for (int j = 0; j < res; j++) {
      sphereX[currVert] = cx[j] * curradius;
      sphereY[currVert] = currY;
      sphereZ[currVert++] = cz[j] * curradius;
    }
    angle += angle_step;
  }
  sDetail = res;
}

// Generic routine to draw textured sphere
void texturedSphere(float r, PImage t) 
{
  int v1,v11,v2;
  r = (r + 240 ) * 0.33;
  beginShape(TRIANGLE_STRIP);
  texture(t);
  float iu = (float)(t.width-1)/(sDetail);
  float iv = (float)(t.height-1)/(sDetail);
  float u = 0, v = iv;
  for (int i = 0; i < sDetail; i++) {
    vertex(0, -r, 0,u,0);
    vertex(sphereX[i] * r, sphereY[i] * r, sphereZ[i] * r, u, v);
    u += iu;
  }
  vertex(0, -r, 0, u, 0);
  vertex(sphereX[0] * r, sphereY[0] * r, sphereZ[0] * r, u, v);
  endShape();   
  
  int voff = 0;
  for(int i = 2; i < sDetail; i++) {
    v1 = v11 = voff;
    voff += sDetail;
    v2 = voff;
    u = 0;
    beginShape(TRIANGLE_STRIP);
    texture(t);
    for(int j = 0; j < sDetail; j++) {
      vertex(sphereX[v1] * r, sphereY[v1] * r, sphereZ[v1++] * r, u, v);
      vertex(sphereX[v2] * r, sphereY[v2] * r, sphereZ[v2++] * r, u, v + iv);
      u += iu;
    }
  
    v1 = v11;
    v2 = voff;
    vertex(sphereX[v1] * r, sphereY[v1] * r, sphereZ[v1] * r, u, v);
    vertex(sphereX[v2] * r, sphereY[v2] * r, sphereZ[v2] * r, u, v + iv);
    endShape();
    v += iv;
  }
  u = 0;
  
  beginShape(TRIANGLE_STRIP);
  texture(t);
  for(int i = 0; i < sDetail; i++) {
    v2 = voff + i;
    vertex(sphereX[v2] * r, sphereY[v2] * r, sphereZ[v2] * r, u, v);
    vertex(0, r, 0, u, v + iv);    
    u += iu;
  }
  vertex(sphereX[voff] * r, sphereY[voff] * r, sphereZ[voff] * r, u, v);
  endShape();
}

