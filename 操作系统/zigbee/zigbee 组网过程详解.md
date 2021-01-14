第一个功能：协调器的组网，终端设备和路由设备发现网络以及加入网络
# 第一步：Z-Stack  由 main（）函数开始执行，main（）函数共做了 2 件事：一是系统初始化，另外一件是开始执行轮转查询式操作系统
```c
 int main( void )                          
{
  .......
  // Initialize the operating system
  osal_init_system();              //第二步，操作系统初始化
......
  osal_start_system();   //初始化完系统任务事件后，正式开始执行操作系统
  ......
} 
```
# 第二步，进入 osal_init_system()函数，执行操作系统初始化
```c
uint8 osal_init_system( void )      //初始化操作系统，其中最重要的是，初始化操作系统的任务
{
  // Initialize the Memory Allocation System
  osal_mem_init();

  // Initialize the message queue
  osal_qHead = NULL;

  // Initialize the timers
  osalTimerInit();

  // Initialize the Power Management System
  osal_pwrmgr_init();

  // Initialize the system tasks.
  osalInitTasks();                 //第三步，执行操作系统任务初始化函数

  // Setup efficient search for the first free block of heap.
  osal_mem_kick();
  return ( SUCCESS );
}
```

# 第三步，进入osalInitTasks()函数，执行操作系统任务初始化
```c
void osalInitTasks( void )       //第三步，初始化操作系统任务
{
  uint8 taskID = 0;
  tasksEvents = (uint16 *)osal_mem_alloc( sizeof( uint16 ) * tasksCnt);
  osal_memset( tasksEvents, 0, (sizeof( uint16 ) * tasksCnt));

  //任务优先级由高向低依次排列，高优先级对应 taskID 的值反而小
  macTaskInit( taskID++ ); //不需要用户考虑
  nwk_init( taskID++ );      //不需要用户考虑
  Hal_Init( taskID++ );      //硬件抽象层初始化，需要我们考虑  
#if defined( MT_TASK )       
  MT_TaskInit( taskID++ );
#endif
  APS_Init( taskID++ );       //不需要用户考虑
#if defined ( ZIGBEE_FRAGMENTATION )  
  APSF_Init( taskID++ );
#endif
  ZDApp_Init( taskID++ );   //第四步，ZDApp层，初始化  ，执行ZDApp_init函数后，如果是协调器将建立网络，如果是终端设备将加入网络。
#if defined ( ZIGBEE_FREQ_AGILITY ) || defined ( ZIGBEE_PANID_CONFLICT )  
  ZDNwkMgr_Init( taskID++ );
#endif
  SerialApp_Init( taskID );  //应用层SerialApp层初始化，需要用户考虑     在此处设置了一个按键触发事件，
                                         //当有按键按下的时候，产生一个系统消息
} 
```                           

# 第四步，进入ZDApp_init()函数，执行ZDApp层初始化
```c
//The first step
void ZDApp_Init( uint8 task_id )     //The first step,ZDApp层初始化。
{
  // Save the task ID
  ZDAppTaskID = task_id;

  // Initialize the ZDO global device short address storage
  ZDAppNwkAddr.addrMode = Addr16Bit;
  ZDAppNwkAddr.addr.shortAddr = INVALID_NODE_ADDR;
  (void)NLME_GetExtAddr();  // Load the saveExtAddr pointer.

  // Check for manual "Hold Auto Start"
  ZDAppCheckForHoldKey();

  // Initialize ZDO items and setup the device - type of device to create.
  ZDO_Init();

  // Register the endpoint description with the AF
  // This task doesn't have a Simple description, but we still need
  // to register the endpoint.
  afRegister( (endPointDesc_t *)&ZDApp_epDesc );

#if defined( ZDO_USERDESC_RESPONSE )
  ZDApp_InitUserDesc();
#endif // ZDO_USERDESC_RESPONSE
 
  // Start the device?
  if ( devState != DEV_HOLD )        //devState 初值为DEV_INIT ， 所以在初始化ZDA层时，就执行该条件语句
  {
    ZDOInitDevice( 0 );     //The second step, 接着转到ZDOInitDevice（）函数，执行The third step;
  }
  else
  {
    // Blink LED to indicate HOLD_START
    HalLedBlink ( HAL_LED_4, 0, 50, 500 );
  }
  ZDApp_RegisterCBs();
}

//The third step,执行ZDOInitDevice()函数，执行设备初始化
uint8 ZDOInitDevice( uint16 startDelay )  //The third step， ZDO层初始化设备，
{
   .......
// Trigger the network start
  ZDApp_NetworkInit( extendedDelay );   //网络初始化，跳到相应的函数里头，执行The fourth step
   .......
}

//The fouth step,执行 ZDApp_NetWorkInit()函数
void ZDApp_NetworkInit( uint16 delay )  //The fourth step,网络初始化
{
  if ( delay )
  {
    // Wait awhile before starting the device
    osal_start_timerEx( ZDAppTaskID, ZDO_NETWORK_INIT, delay );    //发送ZDO_NETWORK_INIT（网络初始化）消息到 ZDApp层，转到                                                                                                                 //ZDApp层，执行The fifth step  ， ZDApp_event_loop() 函数
  }                                                              
  else
  {
    osal_set_event( ZDAppTaskID, ZDO_NETWORK_INIT );
  }
}

//The fifth step,转到ZDApp_event_loop()函数
UINT16 ZDApp_event_loop( uint8 task_id, UINT16 events )
{
if ( events & ZDO_NETWORK_INIT )   //The fivth step，网络初始化事件处理
  {
    // Initialize apps and start the network
    devState = DEV_INIT;

    //设备逻辑类型，启动模式，信标时间，超帧长度，接着转到The sixth step，去启动设备，接着执行The sixth step,转到ZDO_StartDevice()
    ZDO_StartDevice( (uint8)ZDO_Config_Node_Descriptor.LogicalType, devStartMode,  
                     DEFAULT_BEACON_ORDER, DEFAULT_SUPERFRAME_ORDER );

    // Return unprocessed events
    return (events ^ ZDO_NETWORK_INIT);
  }
}

//The sixth step,执行ZDO_StartDevice()函数，启动设备
void ZDO_StartDevice( byte logicalType, devStartModes_t startMode, byte beaconOrder, byte superframeOrder ) //The sixth step
{
......
 if ( ZG_BUILD_COORDINATOR_TYPE && logicalType == NODETYPE_COORDINATOR )   //当设备作为协调器时，执行这个条件语句。
  {
    if ( startMode == MODE_HARD )
    {
      devState = DEV_COORD_STARTING;  

       //向网络层发送网络形成请求。当网络层执行 NLME_NetworkFormationRequest（）建立网络后，将给予 ZDO层反馈信息。
       // 接着转到The seventh step,去执行ZDApp层的  ZDO_NetworkFormationConfirmCB（）函数
      ret = NLME_NetworkFormationRequest( zgConfigPANID, zgApsUseExtendedPANID, zgDefaultChannelList,
                                          zgDefaultStartingScanDuration, beaconOrder,
                                          superframeOrder, false );
    }
if ( ZG_BUILD_JOINING_TYPE && (logicalType == NODETYPE_ROUTER || logicalType == NODETYPE_DEVICE) ) //当为终端设备或路由时
  {
    if ( (startMode == MODE_JOIN) || (startMode == MODE_REJOIN) )
    {
      devState = DEV_NWK_DISC;

      // zgDefaultChannelList与协调器形成网络的通道号匹配。 网络发现请求。
      // 继而转到ZDO_NetworkDiscoveryConfirmCB（）函数
      ret = NLME_NetworkDiscoveryRequest( zgDefaultChannelList, zgDefaultStartingScanDuration );
    }
  }
......
}

//The seventh step，分两种情况，1.协调器   2.路由器或终端设备
 1）协调器
void ZDO_NetworkFormationConfirmCB( ZStatus_t Status ) //The seventh step，给予ZDO层网络形成反馈信息（协调器）
{
osal_set_event( ZDAppTaskID, ZDO_NETWORK_START ); //发送网络启动事件 到 ZDApp层，接着转到ZDApp_event_loop（）函数                                 
......
}

UINT16 ZDApp_event_loop( uint8 task_id, UINT16 events )
{
......
if ( events & ZDO_NETWORK_START )  // 网络启动事件
    {
      ZDApp_NetworkStartEvt();    //网络启动事件，接着跳转到The ninth step, 执行ZDApp_NetworkStartEvt()函数
  ......
    }
}

void ZDApp_NetworkStartEvt( void )     //处理网络启动事件
{
......
 osal_pwrmgr_device( PWRMGR_ALWAYS_ON );                           //电源总是上电
 osal_set_event( ZDAppTaskID, ZDO_STATE_CHANGE_EVT ); //设置网络状态改变事件，发送到ZDApp层，转到The tenth step,去
 ......                                                                                                 // ZDApp_event_loop（）函数，找到相对应的网络改变事件。
}


2)路由器或终端设备
//The seventh step（终端设备）， 当发现有网络存在时，网络层将给予 ZDO 层发现网络反馈信息
ZStatus_t ZDO_NetworkDiscoveryConfirmCB( uint8 ResultCount, networkDesc_t *NetworkList )  
{
   .......
  //把网络发现这个反馈消息，发送到ZDA层，转到 ZDApp_ProcessOSALMsg（），执行
  ZDApp_SendMsg( ZDAppTaskID, ZDO_NWK_DISC_CNF, sizeof(ZDO_NetworkDiscoveryCfm_t), (uint8 *)&msg );
}

void ZDApp_ProcessOSALMsg( osal_event_hdr_t *msgPtr )
{
   ......
   case ZDO_NWK_DISC_CNF:      // (终端设备），网络发现响应。
   ......
          //当发现有网络存在时，网络层将给予 ZDO 层发现网络反馈信息。然后由网络层发起加入网络请求，
          //如加入网络成功，则网络层将给予 ZDO 层加入网络反馈,执行NLME_JoinRequest()函数。然后转到 
          //The ninth step,执行 ZDO_JoinConfirmCB()函数
            if ( NLME_JoinRequest( ((ZDO_NetworkDiscoveryCfm_t *)msgPtr)->extendedPANID,
                 BUILD_UINT16( ((ZDO_NetworkDiscoveryCfm_t *)msgPtr)->panIdLSB, ((ZDO_NetworkDiscoveryCfm_t *)msgPtr)->panIdMSB ),
                 ((ZDO_NetworkDiscoveryCfm_t *)msgPtr)->logicalChannel,
                 ZDO_Config_Node_Descriptor.CapabilityFlags ) != ZSuccess )
            {
              ZDApp_NetworkInit( (uint16)(NWK_START_DELAY
                  + ((uint16)(osal_rand()& EXTENDED_JOINING_RANDOM_MASK))) );
            }
          ......
   }

void ZDO_JoinConfirmCB( uint16 PanId, ZStatus_t Status )  //The ninth step（终端设备）, 终端设备加入网络响应。
{
......
//将ZDO_NWK_JOIN_IND事件发送到ZDA层，执行 ZDApp_ProcessOSALMsg（）函数。
  ZDApp_SendMsg( ZDAppTaskID, ZDO_NWK_JOIN_IND, sizeof(osal_event_hdr_t), (byte*)NULL );
}

void ZDApp_ProcessOSALMsg( osal_event_hdr_t *msgPtr )
{
......
 case ZDO_NWK_JOIN_IND:            //终端设备，加入网络反馈信息事件。
      if ( ZG_BUILD_JOINING_TYPE && ZG_DEVICE_JOINING_TYPE )
      {
        ZDApp_ProcessNetworkJoin(); //转到ZDApp_ProcessNetworkJoin()，执行ZDApp_ProcessNetworkJoin()函数。
      }
      break;
......
} 


在执行ZDApp_ProcessNetworkJoin()函数的时候，要分两种情况，一种是终端设备，一种是路由器：
3)终端设备：
void ZDApp_ProcessNetworkJoin( void )  //处理网络加入事件。
{
......
if ( nwkStatus == ZSuccess )
    {
      //设置 ZDO_STATE_CHANGE_EVT ，发送到ZDA层，执行 ZDApp_event_loop（）函数。
      osal_set_event( ZDAppTaskID, ZDO_STATE_CHANGE_EVT ); 
    }
......
}

4)路由器：
void ZDApp_ProcessNetworkJoin( void )  
{
......
  if ( ZSTACK_ROUTER_BUILD )
        {
          // NOTE: first two parameters are not used, see NLMEDE.h for details
          if ( ZDO_Config_Node_Descriptor.LogicalType != NODETYPE_DEVICE )
          {
            NLME_StartRouterRequest( 0, 0, false );               //路由启动请求
          }
        }
......
}

void ZDO_StartRouterConfirmCB( ZStatus_t Status )
{
  nwkStatus = (byte)Status;
......
  osal_set_event( ZDAppTaskID, ZDO_ROUTER_START );
}


UINT16 ZDApp_event_loop( uint8 task_id, UINT16 events )
{
  if ( events & ZDO_ROUTER_START ) 
    {
      if ( nwkStatus == ZSuccess )
      {
        if ( devState == DEV_END_DEVICE )
          devState = DEV_ROUTER;                         //设备状态变成路由器
  
        osal_pwrmgr_device( PWRMGR_ALWAYS_ON );
      }
      else
      {
        // remain as end device!!
      }
      osal_set_event( ZDAppTaskID, ZDO_STATE_CHANGE_EVT );     //设置ZDO状态改变事件
  
      // Return unprocessed events
      return (events ^ ZDO_ROUTER_START);
    }
}


//The eighth step,执行ZDO状态改变事件
UINT16 ZDApp_event_loop( uint8 task_id, UINT16 events )
{
.......
if ( events & ZDO_STATE_CHANGE_EVT )  //The eighth step， 网络改变事件，这个事件就是在设备加入网络成功后，
                                                                     //并在网络中的身份确定后产生的一个事件
  {
    ZDO_UpdateNwkStatus( devState );  //更新网络状态，转到The eleventh step,执行 ZDO_UpdateNwkStatus（）函数。
  ......
  }
}

//The ninth step,执行ZDO_UpdateNwkStatus()函数，完成网络状态更新
void ZDO_UpdateNwkStatus(devStates_t state)  //The ninth step, 更新网络状态
{
......
      zdoSendStateChangeMsg(state, *(pItem->epDesc->task_id));  //发送状态改变消息到zdo层，这是The tenth step,转到
                                                                                                        //zdoSendStateChangeMsg（）函数
.......
  ZDAppNwkAddr.addr.shortAddr = NLME_GetShortAddr();  //调用NLME_GetShortAddr()函数，获得16位短地址。
  (void)NLME_GetExtAddr();  // Load the saveExtAddr pointer.  //获得64位的IEEE地址。

}

//The tenth step,执行zdoSendStateChangeMsg（）函数
static void zdoSendStateChangeMsg(uint8 state, uint8 taskId) //The tenth step,
{
  osal_event_hdr_t *pMsg = (osal_event_hdr_t *)osal_msg_find(taskId, ZDO_STATE_CHANGE);

  if (NULL == pMsg)
  {
    if (NULL == (pMsg = (osal_event_hdr_t *)osal_msg_allocate(sizeof(osal_event_hdr_t))))
    {
      // Upon failure to notify any EndPoint of the state change, re-set the ZDO event to
      // try again later when more Heap may be available.
      osal_set_event(ZDAppTaskID, ZDO_STATE_CHANGE_EVT);  //如果ZDO状态没有任何改变，再一次，跳到
                                                                                                        //ZDO_STATE_CHANGE_EVT事件处理函数。
    }
    else
    {
      pMsg->event = ZDO_STATE_CHANGE;      //如果ZDO状态改变了 了，把ZDO_STATE_CHANGE这个消息保存到pMsg
      pMsg->status = state;

      (void)osal_msg_send(taskId, (uint8 *)pMsg);    //转到MT_TASK.C，去执行The eleven step, MT_ProcessIncomingCommand()函数
    }
  }
  ......
}
//The eleventh step,去执行MT_ProcessIncomingCommand()函数
void MT_ProcessIncomingCommand( mtOSALSerialData_t *msg )
{
......
case ZDO_STATE_CHANGE:        //The thirteenth step, 接着跳到MT_ZdoStateChangeCB（）函数。 
                                                      //自此，协调器组网形成（终端设备成功加入网络）
         MT_ZdoStateChangeCB((osal_event_hdr_t *)msg);
         break;
......
}
```

# 第五步，//初始化玩系统任务事件后，正是开始执行操作系统，此时操作系统不断的检测有没有任务事件发生，一旦检测到有事件发生，就转 //到相应的处理函数，进行处理。
```c
void osal_start_system( void )  //第五步，正式执行操作系统
{
#if !defined ( ZBIT ) && !defined ( UBIT )
  for(;;)  // Forever Loop     //死循环
#endif
  {
    uint8 idx = 0;
    osalTimeUpdate();
    Hal_ProcessPoll();  // This replaces MT_SerialPoll() and osal_check_timer().
    do {
      if (tasksEvents[idx])  // Task is highest priority that is ready.
      {
        break;        // 得到待处理的最高优先级任务索引号idx
      }
    } while (++idx < tasksCnt);

    if (idx < tasksCnt)
    {
      uint16 events;
      halIntState_t intState;
      HAL_ENTER_CRITICAL_SECTION(intState);  //进入临界区
      events = tasksEvents[idx];             //提取需要处理的任务中的事件
      tasksEvents[idx] = 0;  // Clear the Events for this task.   // 清除本次任务的事件
      HAL_EXIT_CRITICAL_SECTION(intState);   //退出临界区

      events = (tasksArr[idx])( idx, events ); //通过指针调用任务处理函数  ， 紧接着跳到相应的函数去处理，此为第五步

      HAL_ENTER_CRITICAL_SECTION(intState);  //进入临界区
      tasksEvents[idx] |= events;  // Add back unprocessed events to the current task.  // 保存未处理的事件
      HAL_EXIT_CRITICAL_SECTION(intState);   //退出临界区
    }
#if defined( POWER_SAVING )     
    else  // Complete pass through all task events with no activity?
    {
      osal_pwrmgr_powerconserve();  // Put the processor/system into sleep
    }
#endif
  }
}
```