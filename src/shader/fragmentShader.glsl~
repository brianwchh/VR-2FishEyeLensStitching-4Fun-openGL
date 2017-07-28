#version 330 core
in vec3 ourColor;
in vec2 TexCoord;

out vec4 color;

// Texture samplers
uniform sampler2D rightTexture;
uniform sampler2D leftTexture;

#define EQ_H 1080
#define EQ_W 2160



#define PI 3.1415926

#define alfa_step  0.002908882
#define beta_step  0.002908882

#define  xc           475.2897
#define  yc           480.8275
#define  c            0.998948
#define  d           -0.000487
#define  e           0.000037
#define length_invpol  15

#define invpol0 439.859457
#define invpol1 348.592017
#define invpol2 -41.822854
#define invpol3 -22.220878
#define invpol4 131.680083
#define invpol5 -43.335028
#define invpol6 -155.357843
#define invpol7 160.918238
#define invpol8 223.859332
#define invpol9 -164.240550
#define invpol10 -265.376295
#define invpol11 10.178931
#define invpol12   146.861110
#define invpol13 77.086532
#define invpol14 12.744534


//-------------------Right Camera-----------------------------------------------------------

/*
#polynomial coefficients for the inverse mapping function (ocam_model.invpol in MATLAB). These are used by world2cam

439.859457 348.592017 -41.822854 -22.220878 131.680083 -43.335028 -155.357843
160.918238 223.859332 -164.240550 -265.376295 10.178931 146.861110 77.086532 12.744534

#center: "row" and "column", starting from 0 (C convention)

480.827530 475.289705

#affine parameters "c", "d", "e"

0.998948 -0.000487 0.000037
 */





vec2 world2cam_R(vec3 point3D)
{

 float norm        = sqrt(point3D.x*point3D.x + point3D.y*point3D.y);
 float theta       = atan(point3D.z/norm);
 float t, t_i;
 float rho, x, y;
 float invnorm;
 int i;

  vec2 point2D ;

  if (norm != 0)
  {
    invnorm = 1/norm;
    t  = theta;
    rho = invpol0;
    t_i = 1;


    t_i *= t;
    rho += t_i * invpol1;   // theta^1 * invpol1
    t_i *= t;
    rho += t_i * invpol2;   // theta^2 * invpol2
    t_i *= t;
    rho += t_i * invpol3;   // theta^3 * invpol3
    t_i *= t;
    rho += t_i * invpol4;
    t_i *= t;
    rho += t_i * invpol5;
    t_i *= t;
    rho += t_i * invpol6;
    t_i *= t;
    rho += t_i * invpol7;
    t_i *= t;
    rho += t_i * invpol8;
    t_i *= t;
    rho += t_i * invpol9;
    t_i *= t;
    rho += t_i * invpol10;
    t_i *= t;
    rho += t_i * invpol11;
    t_i *= t;
    rho += t_i * invpol12;
    t_i *= t;
    rho += t_i * invpol13;
    t_i *= t;
    rho += t_i * invpol14;



    x = point3D.x*invnorm*rho;
    y = point3D.y*invnorm*rho;

    point2D.x = x*c + y*d + xc;
    point2D.y = x*e + y   + yc;
  }
  else
  {
    point2D.x = xc;
    point2D.y = yc;
  }

  return point2D ;
}


void main()
{

    float x_cord = TexCoord.x * EQ_W ;
    float y_cord = TexCoord.y * EQ_H ;

    float beta = beta_step * (y_cord - EQ_H / 2 * 1) ;    
    float alfa  = alfa_step *(x_cord - EQ_W / 2 * 1)  ;  

    vec3 point3D ;
    vec2 point2D ;
    vec2 p2d_R,p2d_L  ;
    vec4 color_R, color_L ;

    // right source image
        point3D.y = sin(beta) ;        // y
        point3D.x  = cos(beta) * cos(alfa) ;  // x
        point3D.z = cos(beta) * sin(alfa) ;   // z

        point2D = world2cam_R(point3D) / 960 ;   
        p2d_R.x = point2D.y  ;
        p2d_R.y = point2D.x ;

        if(p2d_R.x < 0.0 || p2d_R.x > 1.0 || p2d_R.y < 0 || p2d_R.y > 1.0){
//            color = vec4(0.0,0.0,0.0,0.0) ;
        }
        else {
                color_R = texture(rightTexture, p2d_R);
        }

    // left source image
        float alfa_l  = alfa_step *(x_cord - EQ_W / 2 * 0)  ;  
        point3D.y = sin(beta) ;        // y
        point3D.x  = cos(beta) * cos(alfa_l) ;  // x
        point3D.z = cos(beta) * sin(alfa_l) ;   // z

        point2D = world2cam_R(point3D) / 960 ;   
        p2d_L.x = point2D.y ;
        p2d_L.y = point2D.x ;

        if(p2d_L.x < 0.0 || p2d_L.x > 1.0 || p2d_L.y < 0 || p2d_L.y > 1.0){
//            color = vec4(0.0,0.0,0.0,0.0) ;
        }
        else {
                color_L = texture(leftTexture, p2d_L);
        }

       float blending_xCord_lowBound = alfa_step * (-3) ;
       float blending_xCord_highBound = alfa_step * 50 ;

       if(alfa < blending_xCord_lowBound ) {
           color = color_R ;
       }
       else if(alfa_l > alfa_step * 1080 + blending_xCord_highBound ){
           color = color_L ;
       }
       else {
           float ratio = (alfa - blending_xCord_lowBound )/(blending_xCord_highBound - blending_xCord_lowBound) ;
           color = color_L * ratio + color_R * (1 - ratio)    ;
       }


}
