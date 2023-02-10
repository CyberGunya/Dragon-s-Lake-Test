//
//  game.cpp
//
//  Created by Serhii Hlushenkov on 15.11.2022
//

#include <iostream>
#include <vector>
#include <cmath>
#include <random>
#include "Framework.h"

/* Test Framework realization */

//default window size, if there is no parametrs
int w_w=800, w_h=600;

//scaling value
double scale = 500.0;

//number of blocks
int block_row = 10;
int block_col = 4;
int block_num = block_row*block_col;

//probability of ability drop
std::mt19937 gen32 ((unsigned int)time(0));
double bonus_prob = 0.2;

// main game entities and variables
struct vector
{
    double x,y;
};

class Block;
class Ball;
class Platform;
class Bullet;

class Object{
public:
    Sprite* x;
    double x_pos, y_pos;
    int w, h;
};

class Platform : public Object {
public:
    int direction = 0;
    bool l_is_pressed = false;
    bool r_is_pressed = false;
    double speed;
    double total_boost=1;
    friend bool collide(Bullet, Platform);
    friend bool collide(Ball& ball, Platform plat);
};


class Ball : public Object{
public:
    vector direction;
    double speed;
    bool is_launched = false;
    friend bool collide(Ball&, Block&);
    friend bool collide(Ball& ball, Platform plat);
};

class Block : public Object {
public:
    bool is_displayed = true;
    friend bool collide(Ball&, Block&);
    double direction=0;
};

class Bullet : public Object{
public:
    double speed;
    bool is_launched = false;
    friend bool collide(Bullet, Platform);
};

class Ability : public Object{
public:
    bool is_visible;
    bool is_active=false;
    int active_time;
    double direction=0;
    bool is_good;
    bool is_collided;
};

Platform plat;
Platform turret;
Ball ball;
Object cursor;
Block** arr;
Bullet l_bullet;
Bullet r_bullet;
std::vector<Ability*> boosters;
bool new_game=true;
int count_active;
double default_speed;
int restart_time=-1;
bool player_win;

Object default_plat;
Object neutral_plat;
Object fast_plat;
Object slow_plat;
Object win;
Object lose;

//ball reflection, if collision points are x and y
void reflect(Ball& ball, double x, double y)
{
    if(ball.direction.x>0)
    {
        if(x>ball.x_pos+ball.w/2)
        {
            ball.direction.x=-ball.direction.x;
        }
    }
    else
    {
        if(x<ball.x_pos+ball.w/2)
        {
            ball.direction.x=-ball.direction.x;
        }
    }
    if(ball.direction.y>0)
    {
        if(y>ball.y_pos+ball.h/2)
        {
            ball.direction.y=-ball.direction.y;
        }
    }
    else
    {
        if(y<ball.y_pos+ball.h/2)
        {
            ball.direction.y=-ball.direction.y;
        }
    }
    
}

bool collide(Ball& ball, Block& block)
{
    if(block.is_displayed)
    {
        double min_x = std::fmin(abs(ball.x_pos+ball.w/2 - (block.x_pos+block.w/2)), block.w/2);
        double min_y = std::fmin(abs(ball.y_pos+ball.h/2 - (block.y_pos+block.h/2)), block.h/2);
        double closest_x, closest_y;
        if(ball.x_pos+ball.w/2 - (block.x_pos+block.w/2)>=0)
        {
            closest_x=block.x_pos+block.w/2+min_x;
        }
        else
        {
            closest_x=block.x_pos+block.w/2-min_x;
        }
        if(ball.y_pos+ball.h/2 - (block.y_pos+block.h/2)>=0)
        {
            closest_y=block.y_pos+block.h/2+min_y;
        }
        else
        {
            closest_y=block.y_pos+block.h/2-min_y;
        }
        double dist = sqrt(pow(ball.x_pos+ball.w/2-closest_x,2)+pow(ball.y_pos+ball.h/2-closest_y,2));
        if(dist<=ball.w/2)
        {
            reflect(ball, closest_x, closest_y);
            block.is_displayed=false;
            block.direction=0;
            block_num--;
            return true;
        }
        return false;
    }
    return false;
}

bool collide(Bullet bullet, Platform plat)
{
    if(bullet.x_pos+bullet.w>=plat.x_pos && bullet.x_pos<=plat.x_pos+plat.w)
    {
        return true;
    }
    return false;
}

bool collide(Ball& ball, Platform plat)
{
    double min_x = std::fmin(abs(ball.x_pos+ball.w/2 - (plat.x_pos+plat.w/2)), plat.w/2);
    double min_y = std::fmin(abs(ball.y_pos+ball.h/2 - (plat.y_pos+plat.h/2)), plat.h/2);
    double closest_x, closest_y;
    if(ball.x_pos+ball.w/2 - (plat.x_pos+plat.w/2)>=0)
    {
        closest_x=plat.x_pos+plat.w/2+min_x;
    }
    else
    {
        closest_x=plat.x_pos+plat.w/2-min_x;
    }
    if(ball.y_pos+ball.h/2 - (plat.y_pos+plat.h/2)>=0)
    {
        closest_y=plat.y_pos+plat.h/2+min_y;
    }
    else
    {
        closest_y=plat.y_pos+plat.h/2-min_y;
    }
    double dist = sqrt(pow(ball.x_pos+ball.w/2-closest_x,2)+pow(ball.y_pos+ball.h/2-closest_y,2));
    
    if(dist<=ball.w/2)
    {
        if(closest_y<=ball.y_pos+ball.h/2)
        {
            reflect(ball, closest_x, closest_y);
        }
        else
        {
            double dev = abs(closest_x-(plat.x_pos+plat.w/2));
            dev = 60.0*dev/(plat.w/2.0);
            double angle = 90.0-dev;
            ball.direction.y=-ball.speed*sin(angle*M_PI/180.0);
            if(closest_x>=plat.x_pos+plat.w/2)
            {
                ball.direction.x=sqrt(pow(ball.speed,2)-pow(ball.direction.y,2));
            }
            else
            {
                ball.direction.x=-sqrt(pow(ball.speed,2)-pow(ball.direction.y,2));
            }
            while(ball.y_pos+ball.h>=w_h-plat.h)
            {
                ball.y_pos+=ball.direction.y;
                ball.x_pos+=ball.direction.x;
            }
        }
        return true;
    }
    return false;
}

class MyFramework : public Framework {

public:

    void PreInit(int& width, int& height, bool& fullscreen) override
    {
        width = w_w;
        height = w_h;
        fullscreen = false;
        arr = new Block*[block_col];
        for(int i=0;i<block_col;i++)
        {
            arr[i] = new Block[block_row];
        }
    }

    bool Init() override
    {
        drawTestBackground();
        double x=0;
        double y=w_h/5.0;
        // set object sprites
        if(new_game)
        {
            // blocks
            for(int i=0;i<block_col;i++)
            {
                for(int j=0;j<block_row;j++)
                {
                    switch (i) {
                        case 0:
                            arr[i][j].x = createSprite("01-Breakout-Tiles.png");
                            break;
                        case 1:
                            arr[i][j].x = createSprite("03-Breakout-Tiles.png");
                            break;
                        case 2:
                            arr[i][j].x = createSprite("09-Breakout-Tiles.png");
                            break;
                        case 3:
                            arr[i][j].x = createSprite("07-Breakout-Tiles.png");
                            break;
                        default:
                            break;
                    }
                    setSpriteSize(arr[i][j].x, arr[i][j].w=w_w/10, arr[i][j].h=w_h/25);
                }
            }
            
            // platform
            plat.x = createSprite("47-Breakout-Tiles.png");
            setSpriteSize(plat.x, plat.w=w_w/8, plat.h=w_h/25);
            // ball
            ball.x = createSprite("62-Breakout-Tiles.png");
            setSpriteSize(ball.x, ball.w=w_w/50, ball.h=ball.w);
            // turret
            turret.x = createSprite("Turret.png");
            setSpriteSize(turret.x, turret.w=w_w/8, turret.h=w_h/25);
            // bullets
            l_bullet.x = createSprite("61-Breakout-Tiles.png");
            r_bullet.x = createSprite("61-Breakout-Tiles.png");
            setSpriteSize(l_bullet.x, l_bullet.w=w_w/50, l_bullet.h=w_h/20);
            setSpriteSize(r_bullet.x, r_bullet.w=w_w/50, r_bullet.h=w_h/20);
            //platform variations
            fast_plat.x = createSprite("42-Breakout-Tiles.png");
            slow_plat.x = createSprite("41-Breakout-Tiles.png");
            neutral_plat.x = createSprite("43-Breakout-Tiles.png");
            default_plat.x = createSprite("47-Breakout-Tiles.png");
            setSpriteSize(fast_plat.x, plat.w, plat.h);
            setSpriteSize(slow_plat.x, plat.w, plat.h);
            setSpriteSize(neutral_plat.x, plat.w, plat.h);
            setSpriteSize(default_plat.x, plat.w, plat.h);
            //restart game labels
            win.x = createSprite("Win.png");
            setSpriteSize(win.x, win.w=w_w/3.0, win.h=win.w*((double)w_h/(double)w_w));
            lose.x = createSprite("Lose.png");
            setSpriteSize(lose.x, lose.w=w_w/3.0, lose.h=lose.w*((double)w_h/(double)w_w));
        }
        
        // set blocks position and movement
        for(int i=0;i<block_col;i++)
        {
            x=0;
            for(int j=0;j<block_row;j++)
            {
                if(j%2==0)
                {
                    arr[0][j].direction=(w_h/scale/2)/sqrt(600.0/w_h);
                }
                else
                {
                    arr[0][j].direction=-(w_h/scale/2)/sqrt(600.0/w_h);
                }
                arr[i][j].is_displayed=true;
                arr[i][j].x_pos=x;
                arr[i][j].y_pos=y;
                x+=w_w/10.0;
            }
            y+=w_h/10.0;
        }
        
        // clear abilities vector from previous game
        for(int i=0;i<boosters.size();i++)
        {
            destroySprite(boosters[i]->x);
            delete boosters[i];
        }
        boosters.clear();
        
        // restore all start values and positions
        block_num = block_row*block_col;
        turret.x_pos=w_w/2-turret.w/2;;
        turret.y_pos=0;
        turret.direction=0;
        turret.speed = (w_w/scale/2)/sqrt(600.0/w_h);;
        plat.x_pos = w_w/2-plat.w/2;
        plat.y_pos = w_h-plat.h;
        plat.speed = (w_w/scale)/sqrt(600.0/w_h);;
        plat.total_boost=1;
        plat.x=default_plat.x;
        default_speed = plat.speed;
        ball.x_pos = w_w/2-ball.w/2;
        ball.y_pos = w_h-plat.h-ball.h-1;
        ball.speed = (w_h/scale)/sqrt(600.0/w_h);;
        l_bullet.speed = (1.7*w_h/scale)/sqrt(600.0/w_h);;
        r_bullet.speed = (1.7*w_h/scale)/sqrt(600.0/w_h);;
        ball.direction.x=0;
        ball.direction.y=0;
        ball.is_launched=false;
        l_bullet.is_launched=false;
        r_bullet.is_launched=false;
        new_game=false;
        count_active=0;
        return true;
    }

    void Close() override
    {
        for(int i=0;i<boosters.size();i++)
        {
            destroySprite(boosters[i]->x);
            delete boosters[i];
        }
        boosters.clear();
        for(int i=0;i<block_col;i++)
        {
            for(int j=0;j<block_row;j++)
            {
                destroySprite(arr[i][j].x);
            }
            delete [] arr[i];
        }
        delete [] arr;
        destroySprite(plat.x);
        destroySprite(turret.x);
        destroySprite(ball.x);
        destroySprite(l_bullet.x);
        destroySprite(r_bullet.x);
    }
    void move_plat()
    {
        switch (plat.direction) {
            case 1:
                plat.x_pos+=plat.speed;
                if(plat.x_pos+plat.w>=w_w)
                    plat.x_pos-=plat.speed;
                break;
            case -1:
                plat.x_pos-=plat.speed;
                if(plat.x_pos<0)
                    plat.x_pos=0;
                break;
            default:
                break;
        }
    }
    
    void turret_move(Platform& turret)
    {
        //avoid stucking on the edges
        if(turret.x_pos<=0 || turret.x_pos+turret.w>=w_w)
        {
            turret.direction=-turret.direction;
            while(turret.x_pos<=0 || turret.x_pos+turret.w>=w_w)
            {
                if(turret.direction>0)
                {
                    turret.x_pos+=turret.speed;
                    turret.direction-=turret.speed;
                }
                else
                {
                    turret.x_pos-=turret.speed;
                    turret.direction+=turret.speed;
                }
            }
        }
        else
        {
            if(turret.direction>0)
            {
                turret.x_pos+=turret.speed;
                turret.direction-=turret.speed;
                if(turret.direction<0)
                {
                    turret.direction=0;
                }
            }
            else
            {
                turret.x_pos-=turret.speed;
                turret.direction+=turret.speed;
                if(turret.direction>0)
                {
                    turret.direction=0;
                }
            }
        }
    }
    
    void turret_fire(Platform turret, Platform plat)
    {
        //if at least half of the platform is under the turret - fire
        if(plat.x_pos+plat.w/2>turret.x_pos && plat.x_pos+plat.w/2<turret.x_pos+turret.w)
        {
            if(!l_bullet.is_launched)
            {
                l_bullet.is_launched=true;
                l_bullet.x_pos=turret.x_pos;
                l_bullet.y_pos=turret.y_pos+turret.h;
                r_bullet.is_launched=true;
                r_bullet.x_pos=turret.x_pos+turret.w-r_bullet.w;
                r_bullet.y_pos=turret.y_pos+turret.h;
            }
        }
    }
    
    void move_block(Block& block, int up_lmt, int dwn_lmt)
    {
        if(block.direction<0)
        {
            if(block.y_pos<=up_lmt)
            {
                block.direction=-block.direction;
            }
        }
        else
        {
            for(int j=1;j<block_col;j++)
            {
                // check for collison with visible blocks
                if(block.y_pos+block.h>=dwn_lmt || (block.y_pos+block.h>=arr[j][(int)(block_row*(block.x_pos+block.w/2)/w_w)].y_pos && arr[j][(int)(block_row*(block.x_pos+block.w/2)/w_w)].is_displayed))
                {
                    block.direction=-block.direction;
                    j=block_col;
                }
            }
        }
        block.y_pos+=block.direction;
    }
    
    void move_ball(Ball& ball)
    {
        if(ball.x_pos<=0 || ball.x_pos+ball.h>=w_w)
        {
            ball.direction.x=-ball.direction.x;
            while(ball.x_pos<=0 || ball.x_pos+ball.w>=w_w)
            {
                ball.x_pos+=ball.direction.x;
                ball.y_pos+=ball.direction.y;
            }
        }
        if(ball.y_pos<=0)
        {
            ball.direction.y=-ball.direction.y;
        }
        ball.x_pos+=ball.direction.x;
        ball.y_pos+=ball.direction.y;
    }
    
    void use_ability(Platform& plat, Ability* bonus)
    {
        if(bonus->is_good)
        {
            plat.total_boost+=0.4;
            plat.speed=(default_speed*plat.total_boost);
            if(plat.speed>default_speed*2)
            {
                plat.speed=default_speed*2;
            }
        }
        else
        {
            plat.total_boost-=0.4;
            plat.speed=(default_speed*plat.total_boost);
            if(plat.speed<default_speed*0.5)
            {
                plat.speed=default_speed*0.5;
            }
        }
        if(plat.speed>default_speed+0.1)
        {
            plat.x=fast_plat.x;
        }
        else
        {
            if(plat.speed<default_speed-0.1)
            {
                plat.x=slow_plat.x;
            }
            else
            {
                plat.x=neutral_plat.x;
            }
        }
        bonus->active_time=getTickCount();
    }
    
    void deboost(Platform& plat, Ability* bonus)
    {
        if(bonus->is_good)
        {
            plat.total_boost-=0.4;
        }
        else
        {
            plat.total_boost+=0.4;
        }
        if(plat.total_boost>=2)
        {
            plat.speed=2*default_speed;
        }
        else
        {
            if(plat.total_boost<=0.5)
            {
                plat.speed=0.5*default_speed;
            }
            else
            {
                plat.speed=plat.total_boost*default_speed;
            }
        }
        if(plat.speed>default_speed+0.1)
        {
            plat.x=fast_plat.x;
        }
        else
        {
            if(plat.speed<default_speed-0.1)
            {
                plat.x=slow_plat.x;
            }
            else
            {
                plat.x=neutral_plat.x;
            }
        }
        bonus->is_active=false;
    }
    
    bool Tick() override
    {
        // update platform position
        if(plat.direction!=0)
        {
            move_plat();
        }
        
        // turret random movement
        if(turret.direction==0)
        {
            std::uniform_int_distribution<int> dis((int)-turret.x_pos,(int)(w_w-turret.x_pos));
            turret.direction=dis(gen32)*(600.0/w_w);
        }
        else
        {
            turret_move(turret);
        }
        
        // turret fire check
        if(ball.is_launched)
        {
            turret_fire(turret, plat);
        }
        
        // ball movement
        if(ball.is_launched)
        {
            //check for collide with platform or edge
            if(ball.y_pos+ball.h>=w_h-plat.h-10)
            {
                if(ball.y_pos+ball.h/2>=w_h)
                {
                    // ball is lost - you lose
                    restart_time = getTickCount();
                    player_win=false;
                    Init();
                    return false;
                }
                if(collide(ball, plat))
                {
                    if(block_num==0)
                    {
                        // all blocks are destroyed - you win
                        restart_time = getTickCount();
                        player_win=true;
                        Init();
                        return false;
                    }
                }
            }
            move_ball(ball);
        }
        else
        {
            ball.x_pos=plat.x_pos+plat.w/2-ball.w/2;
        }
        
        // visible blue blocks movement
        for(int i=0;i<block_row;i++)
        {
            if(arr[0][i].is_displayed)
            {
                move_block(arr[0][i], turret.y_pos+turret.h+5, arr[block_col-1][0].y_pos+arr[block_col-1][0].h);
            }
        }
        
        // ball and block colission check
        for(int i=0;i<block_col;i++)
        {
            for(int j=0;j<block_row;j++)
            {
                if(collide(ball, arr[i][j]))
                {
                    std::uniform_real_distribution<double> dis (0,1);
                    if(dis(gen32)<=bonus_prob)      //check for ability spawn
                    {
                        // spawn new random ability
                        Ability* bonus = new Ability();
                        if(dis(gen32)<=0.5)
                        {
                            bonus->x=createSprite("slow-ability.png");
                            bonus->is_good=false;
                        }
                        else
                        {
                            bonus->x=createSprite("fast-ability.png");
                            bonus->is_good=true;
                        }
                        setSpriteSize(bonus->x, bonus->w=arr[i][j].w/2, bonus->h=arr[i][j].h/2);
                        bonus->direction=(w_h/scale/2)/sqrt(600.0/w_h);
                        bonus->x_pos=arr[i][j].x_pos+arr[i][j].w/2-bonus->w/2;
                        bonus->y_pos=arr[i][j].y_pos+arr[i][j].h/2-bonus->h/2;
                        bonus->is_collided=false;
                        bonus->is_visible=true;
                        boosters.push_back(bonus);
                    }
                }
                if(arr[i][j].is_displayed)
                {
                    drawSprite(arr[i][j].x, arr[i][j].x_pos, arr[i][j].y_pos);
                }
            }
        }
        
        //check for active abilities
        for(int i=0;i<boosters.size();i++)
        {
            if(boosters[i]->is_active)
            {
                if(getTickCount() - boosters[i]->active_time>=20000)
                {
                    // if 20 second passed, stop the ability
                    deboost(plat, boosters[i]);
                    count_active--;
                }
            }
        }
        if(count_active==0)
        {
            plat.x=default_plat.x;
        }
        
        //check abilities for collisions
        for(int i=0;i<boosters.size();i++)
        {
            //get ability column number
            int col = block_row*(boosters[i]->x_pos+boosters[i]->w/2)/w_w;
            //collide with blocks
            for(int j=0;j<block_col;j++)
            {
                if(arr[j][col].y_pos>boosters[i]->y_pos)
                {
                    if(boosters[i]->y_pos+boosters[i]->h>=arr[j][col].y_pos && arr[j][col].is_displayed)
                    {
                        boosters[i]->is_collided=true;
                    }
                }
            }
            
            //check if ability catched by player
            if(boosters[i]->y_pos+boosters[i]->h>=w_h-plat.h && boosters[i]->is_visible && boosters[i]->y_pos<w_h)
            {
                if(boosters[i]->x_pos+boosters[i]->w>=plat.x_pos && boosters[i]->x_pos<=plat.x_pos+plat.w)
                {
                    boosters[i]->is_visible=false;
                    boosters[i]->is_collided=true;
                    use_ability(plat, boosters[i]);
                    count_active++;
                    boosters[i]->is_active=true;
                }
            }
            
            // move ability, if there is no collision
            if(!boosters[i]->is_collided)
            {
                boosters[i]->y_pos+=boosters[i]->direction;
            }
            boosters[i]->is_collided=false;
        }
    
        
        // check for bullet and platform collision
        if(l_bullet.y_pos+l_bullet.h>=plat.y_pos && l_bullet.is_launched)
        {
            if(collide(l_bullet, plat) || collide(r_bullet, plat))
            {
                //you get shot - you lose
                restart_time = getTickCount();
                player_win=false;
                Init();
                return false;
            }
        }
        
        // bullet movement
        if(l_bullet.is_launched)
        {
            l_bullet.y_pos+=l_bullet.speed;
            r_bullet.y_pos+=r_bullet.speed;
            if(l_bullet.y_pos>=w_h)
            {
                l_bullet.is_launched=false;
                r_bullet.is_launched=false;
            }
            else
            {
                drawSprite(l_bullet.x, l_bullet.x_pos, l_bullet.y_pos);
                drawSprite(r_bullet.x, r_bullet.x_pos, r_bullet.y_pos);
            }
        }
        
        //textures drawing
        drawSprite(plat.x, plat.x_pos, plat.y_pos);
        drawSprite(ball.x, ball.x_pos, ball.y_pos);
        drawSprite(turret.x, turret.x_pos, turret.y_pos);
        for (int i=0; i<boosters.size(); i++)
        {
            if(boosters[i]->is_visible)
            {
                drawSprite(boosters[i]->x, boosters[i]->x_pos, boosters[i]->y_pos);
            }
        }
        // draw after restart
        if(restart_time>0 && getTickCount()-restart_time<800)
        {
            if(player_win)
            {
                drawSprite(win.x, w_w/2-win.w/2, w_h/2-win.h/2);
            }
            else
            {
                drawSprite(lose.x, w_w/2-lose.w/2, w_h/2-lose.h/2);
            }
        }
        return false;
    }

    void onMouseMove(int x, int y, int xrelative, int yrelative) override
    {
        cursor.x_pos=x;
        cursor.y_pos=y;
    }

    void onMouseButtonClick(FRMouseButton button, bool isReleased) override
    {
        if(button==FRMouseButton::LEFT)
        {
            if(!ball.is_launched)
            {
                ball.direction.x=(cursor.x_pos-ball.x_pos)/scale;
                ball.direction.y=(cursor.y_pos-ball.y_pos)/scale;
                double temp = sqrt(pow(ball.speed, 2)/(pow(ball.direction.x, 2)+pow(ball.direction.y, 2)));
                ball.direction.x*=temp;
                ball.direction.y*=temp;
                ball.is_launched=true;
            }
        }
    }

    void onKeyPressed(FRKey k) override
    {
        if(k==FRKey::RIGHT)
        {
            if(plat.x_pos+plat.w<w_w)
            {
                plat.x_pos+=plat.speed;
                plat.direction=1;
                plat.r_is_pressed = true;
            }
        }
        if(k==FRKey::LEFT)
        {
            plat.x_pos-=plat.speed;
            plat.direction = -1;
            plat.l_is_pressed = true;
        }
        return;
    }

    void onKeyReleased(FRKey k) override
    {
        if(k==FRKey::RIGHT)
        {
            plat.r_is_pressed=false;
            if(plat.l_is_pressed==true)
            {
                plat.direction=-1;
                return;
            }
            else
            {
                plat.direction=0;
                return;
            }
        }
        if(k==FRKey::LEFT)
        {
            plat.l_is_pressed=false;
            if(plat.r_is_pressed==true)
            {
                plat.direction=1;
                return;
            }
            else
            {
                plat.direction=0;
                return;
            }
        }
        return;
    }
    
    const char* GetTitle() override
    {
        return "Arcanoid";
    }
};

void atoi(const char val[], int& w, int& h)
{
    const char* p = val;
    int rez=0;
    while(*p!='x')
    {
        rez*=10;
        rez+=(int)*p++-48;
    }
    p++;
    w=rez;
    rez=0;
    while(*p!='\0')
    {
        rez*=10;
        rez+=(int)*p++-48;
    }
    h=rez;
}

int main(int argc, char *argv[])
{
    if(argc==3)
    {
        char* command = argv[argc-1];
        atoi(command,w_w,w_h);
    }
    return run(new MyFramework);
}
