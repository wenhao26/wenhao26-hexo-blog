---
title: PHP上传文件到AWS-S3
categories: 
- 开发技能
tags: 
- PHP
- S3
---

##### Amazon Simple Storage Service (Amazon S3) 是一种对象存储服务，提供行业领先的可扩展性、数据可用性、安全性和性能。各种规模和行业的客户可以为几乎任何使用案例存储和保护任意数量的数据，例如数据湖、云原生应用程序和移动应用程序。通过经济高效的存储类和易于使用的管理功能，您可以优化成本、组织数据并配置精细调整过的访问控制，从而满足特定的业务、组织和合规性要求。
<!--more-->

#### 申请开通 AWS-S3 服务

##### 在应用中心获取配置（如何申请本文章就不细说啦），下面是我整理一些配置项，仅供参考。

``` bash
;AWS-S3配置
S3_URL      = 'https://xxxxx.s3.us-west-1.amazonaws.com/'
S3_BUCKET   = 'xxxxxxxxx'
S3_ENDPOINT = 'us-west-1'
S3_KEY      = 'xxxxxxxxxxxxxxxxxxxxxx'
S3_SECRET   = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'
```

#### 如何在PHP项目中使用

##### 首先我们还是要安装一下官方提供的SDK，使用 composer 下载包即可。[Github地址](github：https://github.com/aws/aws-sdk-php)

``` bash
composer require aws/aws-sdk-php
```

#### 开始写个例子

##### 下面是我写了一个静态方法来使用S3的相关功能，实例项目使用，你可以根据你使用的PHP框架，如：laravel、yii等，这些都会有集成的类库。

``` php
/**
 * 上传图片到AWS-S3
 *
 * @param File   $file        文件
 * @param string $storageName 存储名称
 * @return array
 */
public static function uploadImgToAWSS3(File $file, string $storageName)
{
    try {
        validate([
            'file' => [
                'fileSize' => 10485760,
                'fileExt'  => 'jpg,png,gif,jpeg',
                'fileMime' => 'image/jpeg,image/png,image/gif',
            ]
        ])->check(['file' => $file]);
    } catch (ValidateException $e) {
        echo $e->getError();die;
    }

    $group     = "{$storageName}/" . date('Ymd') . '/'; // 文件夹作为分组对象处理
    $realPath  = $file->getRealPath(); // 上传文件资源路径
    $extension = $file->extension(); // 文件后缀
    $filename  = $group . md5_file($file->getRealPath()) . '.' . $extension; // 新地址文件名称

    $credentials = new Credentials(env('S3_KEY'), env('S3_SECRET'));
    $s3 = new S3Client([
        'version'     => 'latest',
        'region'      => env('AWS.S3_ENDPOINT'), // AWS区域和终端节点
        'credentials' => $credentials, // 加载证书
        'debug'       => false // 开启debug模式
    ]);
    $bucket = env('S3_BUCKET'); // AWS存储桶名称
    $source = $realPath; // 需要上传的文件

    // 分段上传
    $uploader = new MultipartUploader($s3, $source, [
        'bucket' => $bucket,
        'key'    => $filename,
        'ACL'    => 'public-read', // 设置访问权限公开，不然访问不了

        'before_initiate' => function (Command $command) {
            $command['CacheControl'] = 'max-age=3600';
        },
        'before_upload'   => function (Command $command) {
            $command['RequestPayer'] = 'requester';
        },
        'before_complete' => function (Command $command) {
            $command['RequestPayer'] = 'requester';
        },
    ]);

    try {
        $result = $uploader->upload();
        return [
            'path' => urldecode($result['Key']),
            'url'  => urldecode($result['ObjectURL'])
        ];
    } catch (MultipartUploadException $e) {
        // 上传失败，返回错误信息
        $uploader = new MultipartUploader($s3, $source, [
            'state' => $e->getState(),
        ]);
        echo $e->getMessage();die;
    }
}
```