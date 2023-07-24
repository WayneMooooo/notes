# MPC程序流程
```graphviz
digraph graph_name{
    bgcolor="transparent";//背景透明
    main -> get_course;
    main -> calc_speed_profile;
    main -> do_simulation;
    do_simulation -> smooth_yaw;
    do_simulation -> calc_nearest_index
    do_simulation -> calc_ref_trajectory;
    do_simulation -> iterative_linear_mpc_control;
    iterative_linear_mpc_control -> predict_motion;
    iterative_linear_mpc_control -> linear_mpc_control;
    }
```