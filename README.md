
## Udacity Self Driving Car Nanodegree - Term 3 - Project 1 --Path Planning

### Objective of the project
 We design a path planning system to generate a path for car to follow smoothly, safely move between three lane highway.

### Rubric Goal
1. No accident during 4.32 miles of driving. 
2. Do not exceed the speed limit.
3. Do not exceed the Acceleration and Jerk limit.
4. Do not hit another car.
5. Stay inside lane.
6. Able to change lanes.

### Achieved Rubric 
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
* Both of this are way to control the acceleration. Either set a limitation of the acceleration or using average of previous acceleration, so the change of acceleration will under the limit.

```C++
if(switch_lane)
{
  // reduce switch lane again too fast
  if (car_d < (2 + 4 * lane + 0.5) && car_d > (2 + 4 * lane - 0.5))
  {
    if (left_lane_ok && (car_dist > safety_dist))
    {
      lane = lane -1;
    }
    else if (right_lane_ok && (car_dist > safety_dist))
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

5. The car will stay inside the lane, except switching between lane. And the car is won't driving to the outside of the road base on the switch lane model.

6. The car is able to switch between lanes. The car will change lanes when slow traffic ahead and other lanes is safe to switch.

### Reflection about model to generate path
* The path planning model is create through a series of steps to generate smooth path. It starts by extracting the current vehicle state: Cartesian coordinates x, y and yaw; Frenet coordinates s and d; current speed of the vehicle. And then, it takes the unused part of the previous path and fills with addition waypoints to complete the path base on the decision made by sensor fusion data.

* To decide the next move of our vehicle, our vehicle uses the information from the sensor fusion data, which contains other vehicles nearby. First, Our vehicle compare the information with the nearby vehicles, and check if any vehicles is blocked our current lane. Then it checks the safety of moving to another lanes. 
If no other lane is safe to switch, it will remains on the current lane and keeps the same speed as the vehicle in front until other lanes safe to move on.

* In order to make the lane switch experience better, it checks little bit more ahead on the other lane. If both side lane is safe to switch, it will also choose the lane which the car is far away from the our vehicle. 

* After our vehicle decides to switch lane, it generates three point in 30, 60 and 90 distance. and passing this three waypoints to a spline from the C++ spline library(http://kluge.in-chemnitz.de/opensource/spline/) to generate a smooth path.

* Finally, the new waypoints are added to the existing waypoints until the max waypoints we set. 

### Improvement
* Right now, the accelerate of the vehicle is control by variable, which sometime may causing unexpected outcome. Some of the variable is chosen by trying and only work in some condition. It will be better is we can generate a cost function that can meet all different condition. 

* We also can imply JMT(Jerk Minimum Trajectory) to choose the best trajectory. We currently using different variable to control the trajectory and limit the jerk. It will have similar jerk every time it switch lane. And it is not always the best trajectory with the minimum trajectory.  

* After this kind of improvement, our vehicle can preform better is different kind of situation. 







