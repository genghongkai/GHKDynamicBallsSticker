# GHKDynamicBallsSticker
动态球贴纸，仿摩拜单车贴纸，来自 https://hanjunqiang.github.io


//
//  JQStickersView.h
//  JQStickersView
//
//  Created by 韩俊强 on 2017/6/16.
//  Copyright © 2017年 HaRi. All rights reserved.
//

#import <UIKit/UIKit.h>

@interface JQStickersView : UIView

/**
 Initialization JQStickersView

 @param frame frame description
 @param ballDiameter ballDiameter description
 @param imgArray imgArray description
 @return return value description
 */
- (instancetype)initWithFrame:(CGRect)frame andBallDiameter:(CGFloat)ballDiameter andImgArray:(NSMutableArray *)imgArray;


// Stop DeviceMotionUpdate
- (void)stopDeviceMotionUpdate;

@end
//
//  JQStickersView.m
//  JQStickersView
//
//  Created by 韩俊强 on 2017/6/16.
//  Copyright © 2017年 HaRi. All rights reserved.
//

#import "JQStickersView.h"
#import <CoreMotion/CoreMotion.h>

@interface JQStickersView()

@property (nonatomic, strong) NSMutableArray *ballsArray;
@property (nonatomic, strong) UIGravityBehavior *gravityBeahvior;
@property (nonatomic, strong) UIGravityBehavior *gravity;
@property (nonatomic, strong) UICollisionBehavior *collision;
@property (nonatomic, strong) UIDynamicAnimator *animator;
@property (nonatomic, strong) UIDynamicItemBehavior *dynamicItemBehavior;
@property (nonatomic) CMMotionManager *motionManager;

@end
@implementation JQStickersView

- (instancetype)initWithFrame:(CGRect)frame andBallDiameter:(CGFloat)ballDiameter andImgArray:(NSMutableArray *)imgArray
{
    if (self == [super initWithFrame:frame]) {
        if (!ballDiameter) {
            ballDiameter = 40;
        }
        [self initBallsViewAndImgArray:imgArray andBallDiameter:ballDiameter];
        [self initGyroManager];
    }
    return self;
}

- (void)initBallsViewAndImgArray:(NSMutableArray *)imgArr andBallDiameter:(CGFloat)ballDiameter
{
    self.ballsArray  = [NSMutableArray array];
    
    //Add two balls, use the  gravity characteristics and collision characteristics
    
    for (int i = 0; i<imgArr.count; i++) {
        UIImageView *ballImageView = [UIImageView new];
        // ballColor
        ballImageView.image = [UIImage imageNamed:[NSString stringWithFormat:@"%@", imgArr[i]]];
        // ball size
        ballImageView.layer.cornerRadius = ballDiameter / 2;
        ballImageView.layer.masksToBounds = YES;
        
        // ball's location
        CGRect frameRect = CGRectMake(arc4random()%((int)(self.bounds.size.width - ballDiameter)), 0, ballDiameter, ballDiameter);
        ballImageView.frame = frameRect;
        
        // add superView
        [self addSubview:ballImageView];
        
        // add balls
        [self.ballsArray addObject:ballImageView];
    }
    _animator = [[UIDynamicAnimator alloc]initWithReferenceView:self];
    
    // add gravity characteristics
    _gravity = [[UIGravityBehavior alloc]initWithItems:self.ballsArray];
    [self.animator addBehavior:_gravity];
    
    // add collision characteristics
    _collision = [[UICollisionBehavior alloc]initWithItems:self.ballsArray];
    _collision.translatesReferenceBoundsIntoBoundary = YES;
    [self.animator addBehavior:_collision];
    
    // add elasticity
    UIDynamicItemBehavior *dynamicItemBehavior = [[UIDynamicItemBehavior alloc]initWithItems:self.ballsArray];
    dynamicItemBehavior.allowsRotation = YES; //  allow rotation
    dynamicItemBehavior.elasticity = 0.6; // elastic value
    [self.animator addBehavior:dynamicItemBehavior];
}

// init GyroManager
- (void)initGyroManager
{
    self.motionManager = [[CMMotionManager alloc]init];
    self.motionManager.deviceMotionUpdateInterval = 0.01;
    
    
    [self.motionManager startDeviceMotionUpdatesToQueue:[NSOperationQueue currentQueue] withHandler:^(CMDeviceMotion * _Nullable motion, NSError * _Nullable error) {
        double rotation = atan2(motion.attitude.pitch, motion.attitude.roll);
        self.gravity.angle = rotation;
    }];
}

- (void)stopDeviceMotionUpdate
{
    [self.motionManager stopDeviceMotionUpdates];
}

- (void)dealloc
{
    [self.motionManager stopDeviceMotionUpdates];
}


/*
// Only override drawRect: if you perform custom drawing.
// An empty implementation adversely affects performance during animation.
- (void)drawRect:(CGRect)rect {
    // Drawing code
}
*/

@end

//
//  ViewController.m
//  JQStickersView
//
//  Created by 韩俊强 on 2017/6/16.
//  Copyright © 2017年 HaRi. All rights reserved.
//

#import "ViewController.h"
#import "JQStickersView.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    
    
}

- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];
    
    NSMutableArray *imgArray = [NSMutableArray arrayWithArray:@[@"testImage_1",@"testImage_2",@"testImage_3",@"testImage_4",@"testImage_5",@"testImage_6",@"testImage_7",@"testImage_8",@"testImage_1",@"testImage_2",@"testImage_3",@"testImage_4"]];
    
    JQStickersView *jqStickersView = [[JQStickersView alloc]initWithFrame:CGRectMake(0, 80, [UIScreen mainScreen].bounds.size.width, 200) andBallDiameter:40 andImgArray:imgArray];
    jqStickersView.backgroundColor = [UIColor cyanColor];
    [self.view addSubview:jqStickersView];
    
//    [jqStickersView stopDeviceMotionUpdate];
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}


@end
