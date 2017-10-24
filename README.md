
## Udacity Self Driviing Car Nanodegree - Term 3 - Project 1 --Path Planning

### Objective of the project
 We design a path planning system to generate a path for car to follow smoothly, safely move between three lane highway.

### rubric Goal
1. no accident during 4.32 miles of driving. 
2. do not exceed the speed limit.
3. do not exceed the Acceleration and Jerk limit.
4. do not hit another car.
5. stay inside lane.
6. able to change lanes.

### Achieved rubric 
1. The car is able to drive over 15 miles without incident. During this 15 miles the car is able to change lane and passing slower traffic and able to slow down and to match the speed to the traffic's speed when switch lane is not safe.
<img src = "./RM_image/image1.png">

2. The car is always try to increase the speed to the speed limit. Unless the condition is not safe.
```C++
  double acc_cost = ref_vel_max/(car_dist * car_dist);
  double acc_increase = 0.2 + acc_cost;
  else if(ref_vel < ref_vel_max)
  {
    if (acc_increase > 1.0)
    {
      acc_increase =(abs(ref_vel_old-ref_vel) + acc_increase)/2;
    } 
    ref_vel += acc_increase;
  }
  ```
* As long as the current speed is less than the speed limit, it will keep increase. The idea of acc_cost is to control the speed using the distance from the car in front of us. 

3. The car keeps the acceleration and jerk under the limit.
```C++
if (adjust_acc > 1.0)
{
  adjust_acc = 1.0;
}
```
* And 
```C++
if (acc_increase > 1.0)
{
  acc_increase =(abs(ref_vel_old-ref_vel) + acc_increase)/2;
} 
```
* Both of this are way to control the acceleration. Either set a limitation of the acceleration or using average of pervious acceleration, so the change of acceleration will under the limit.

```C++
if(switch_lane)
{
  // reduce switch lane again too fast
  if (car_d < (2 + 4 * lane + 0.5) && car_d > (2 + 4 * lane - 0.5))
  {
    if (left_lane_ok && (car_dist > safty_dist))
    {
      lane = lane -1;
    }
    else if (right_lane_ok && (car_dist > safty_dist))
    {
      lane = lane +1;
    }
  }
}
```
* For Jerk limitation, I set another condition before the car switch between line. The car will not switch lane until the car is at the center of the lane. Most of the time, Jerk over limit when it try to switch two lane at a time.

4. The car will avoid collision, with other car.
```C++ 
if (d < (2 + 4 * lane +2) && d > (2 + 4 * lane -2))
{                    
 car_dist = check_car_s - car_s;
 if ((check_car_s > car_s) && ((check_car_s - car_s) < 30))
  {
    too_close = true;
    switch_lane = true;
  }
}
```
* To avoid collision. I use the sensor information and locate any car that is in front of the car, and active too_close flag. Base on the distance, I reduce the speed of the car. As long as the react-time is enough, it also can handle other car suddenly cut in front of the car.

5. The car will stay inside the lane, except swtiching between lane. And the car is won't driving to the outside of the road base on the switch lane model.

6. The car is able to switch between lanes. The car will change lanes when slow traffic ahead and other lanes is safe to switch.









