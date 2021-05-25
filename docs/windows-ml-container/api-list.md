---
title: API List
description: Explore the APIs approved for use in the Windows ML container
ms.date: 10/14/2019
ms.topic: article
keywords: windows 10, windows ml container, container, iot, edge
ms.localizationpriority: medium
---

# API List

APIs exported by [OneCore.lib umbrella library](https://docs.microsoft.com/windows/win32/apiindex/umbrella-lib-onecore) are available for native applications in the Windows ML container.

Due to the small size of Windows ML container, only a subset of WinRT APIs are available in the container image.  The list below is intended to exhaustively list the types included in the base OS image.  If you need a type that's not included, please mail winmlcfb@microsoft.com.

## Windows.AI

### [Windows.AI.MachineLearning](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning)

ILearningModelFeatureDescriptor </br> ILearningModelFeatureValue </br> ILearningModelOperatorProvider </br> ITensor </br> ImageFeatureDescriptor </br> ImageFeatureValue </br> LearningModel </br> LearningModelBinding </br> LearningModelDevice </br> LearningModelDeviceKind </br> LearningModelEvaluationResult </br> LearningModelFeatureKind </br> LearningModelSession </br> LearningModelSessionOptions </br> MachineLearningContract </br> MapFeatureDescriptor </br> SequenceFeatureDescriptor </br> TensorBoolean </br> TensorDouble </br> TensorFeatureDescriptor </br> TensorFloat </br> TensorFloat16Bit </br> TensorInt16Bit </br> TensorInt32Bit </br> TensorInt64Bit </br> TensorInt8Bit </br> TensorKind </br> TensorString </br> TensorUInt16Bit </br> TensorUInt32Bit </br> TensorUInt64Bit </br> TensorUInt8Bit

## Windows.ApplicationModel

### [Windows.ApplicationModel.Background](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background)

DeviceWatcherTrigger </br> IBackgroundTrigger

## Windows.Data

### [Windows.Data.Html](https://docs.microsoft.com/uwp/api/Windows.Data.Html)

HtmlUtilities

### [Windows.Data.Json](https://docs.microsoft.com/uwp/api/Windows.Data.Json)

IJsonValue </br> JsonArray </br> JsonError </br> JsonErrorStatus </br> JsonObject </br> JsonValue </br> JsonValueType

### [Windows.Data.Text](https://docs.microsoft.com/uwp/api/Windows.Data.Text)

AlternateNormalizationFormat </br> AlternateWordForm </br> SelectableWordSegment </br> SelectableWordSegmentsTokenizingHandler </br> SelectableWordsSegmenter </br> SemanticTextQuery </br> TextConversionGenerator </br> TextPhoneme </br> TextPredictionGenerator </br> TextPredictionOptions </br> TextReverseConversionGenerator </br> TextSegment </br> UnicodeCharacters </br> UnicodeGeneralCategory </br> UnicodeNumericType </br> WordSegment </br> WordSegmentsTokenizingHandler </br> WordsSegmenter

### [Windows.Data.Xml.Dom](https://docs.microsoft.com/uwp/api/Windows.Data.Xml.Dom)

DtdEntity </br> DtdNotation </br> IXmlCharacterData </br> IXmlNode </br> IXmlNodeSelector </br> IXmlNodeSerializer </br> IXmlText br> NodeType </br> XmlAttribute </br> XmlCDataSection </br> XmlComment </br> XmlDocument </br> XmlDocumentFragment </br> XmlDocumentType </br> XmlDomImplementation </br> XmlElement </br> XmlEntityReference </br> XmlLoadSettings </br> XmlNamedNodeMap </br> XmlNodeList </br> XmlProcessingInstruction </br> XmlText

### [Windows.Data.Xml.Xsl](https://docs.microsoft.com/uwp/api/Windows.Data.Xml.Xsl)

XsltProcessor

## Windows.Devices

### [Windows.Devices](https://docs.microsoft.com/uwp/api/windows.devices)

ILowLevelDevicesAggregateProvider </br> LowLevelDevicesAggregateProvider </br> LowLevelDevicesController

### [Windows.Devices.Adc](https://docs.microsoft.com/uwp/api/windows.devices.adc)

AdcChannel </br> AdcChannelMode </br> AdcController

### [Windows.Devices.Adc.Provider](https://docs.microsoft.com/uwp/api/windows.devices.adc.provider)

ProviderAdcChannelMode

### [Windows.Devices.Background](https://docs.microsoft.com/uwp/api/windows.devices.background)

DeviceServicingDetails </br> DeviceUseDetails

### [Windows.Devices.Bluetooth](https://docs.microsoft.com/uwp/api/Windows.Devices.Bluetooth)

BluetoothAdapter </br> BluetoothAddressType </br> BluetoothCacheMode </br> BluetoothClassOfDevice </br> BluetoothConnectionStatus </br> BluetoothDevice </br> BluetoothDeviceId </br> BluetoothError </br> BluetoothLEAppearance </br> BluetoothLEAppearanceCategories </br> BluetoothLEAppearanceSubcategories </br> BluetoothLEDevice </br> BluetoothMajorClass </br> BluetoothMinorClass </br> BluetoothServiceCapabilities </br> BluetoothSignalStrengthFilter </br> BluetoothUuidHelper

### [Windows.Devices.Bluetooth.Advertisement](https://docs.microsoft.com/uwp/api/Windows.Devices.Bluetooth.Advertisement)

BluetoothLEAdvertisement </br> BluetoothLEAdvertisementBytePattern </br> BluetoothLEAdvertisementDataSection </br> BluetoothLEAdvertisementDataTypes </br> BluetoothLEAdvertisementFilter </br> BluetoothLEAdvertisementFlags </br> BluetoothLEAdvertisementPublisher </br> BluetoothLEAdvertisementPublisherStatus </br> BluetoothLEAdvertisementPublisherStatusChangedEventArgs </br> BluetoothLEAdvertisementReceivedEventArgs </br> BluetoothLEAdvertisementType </br> BluetoothLEAdvertisementWatcher </br> BluetoothLEAdvertisementWatcherStatus </br> BluetoothLEAdvertisementWatcherStoppedEventArgs </br> BluetoothLEManufacturerData </br> BluetoothLEScanningMode

### [Windows.Devices.Bluetooth.Background](https://docs.microsoft.com/uwp/api/Windows.Devices.Bluetooth.Background)

BluetoothEventTriggeringMode </br> BluetoothLEAdvertisementPublisherTriggerDetails </br> BluetoothLEAdvertisementWatcherTriggerDetails </br> GattCharacteristicNotificationTriggerDetails </br> GattServiceProviderConnection </br> GattServiceProviderTriggerDetails </br> RfcommConnectionTriggerDetails </br> RfcommInboundConnectionInformation </br> RfcommOutboundConnectionInformation

### [Windows.Devices.Bluetooth.GenericAttributeProfile](https://docs.microsoft.com/uwp/api/Windows.Devices.Bluetooth.GenericAttributeProfile)

GattCharacteristic </br> GattCharacteristicProperties </br> GattCharacteristicUuids </br> GattCharacteristicsResult </br> GattClientCharacteristicConfigurationDescriptorValue </br> GattClientNotificationResult </br> GattCommunicationStatus </br> GattDescriptor </br> GattDescriptorUuids </br> GattDescriptorsResult </br> GattDeviceService </br> GattDeviceServicesResult </br> GattLocalCharacteristic </br> GattLocalCharacteristicParameters </br> GattLocalCharacteristicResult </br> GattLocalDescriptor </br> GattLocalDescriptorParameters </br> GattLocalDescriptorResult </br> GattLocalService </br> GattOpenStatus </br> GattPresentationFormat </br> GattPresentationFormatTypes </br> GattProtectionLevel </br> GattProtocolError </br> GattReadClientCharacteristicConfigurationDescriptorResult </br> GattReadRequest </br> GattReadRequestedEventArgs </br> GattReadResult </br> GattReliableWriteTransaction </br> GattRequestState </br> GattRequestStateChangedEventArgs </br> GattServiceProvider </br> GattServiceProviderAdvertisementStatus </br> GattServiceProviderAdvertisementStatusChangedEventArgs </br> GattServiceProviderAdvertisingParameters </br> GattServiceProviderResult </br> GattServiceUuids </br> GattSession </br> GattSessionStatus </br> GattSessionStatusChangedEventArgs </br> GattSharingMode </br> GattSubscribedClient </br> GattValueChangedEventArgs </br> GattWriteOption </br> GattWriteRequest </br> GattWriteRequestedEventArgs </br> GattWriteResult

### [Windows.Devices.Bluetooth.Rfcomm](https://docs.microsoft.com/uwp/api/Windows.Devices.Bluetooth.Rfcomm)

RfcommDeviceService </br> RfcommDeviceServicesResult </br> RfcommServiceId </br> RfcommServiceProvider

### [Windows.Devices.Custom](https://docs.microsoft.com/uwp/api/Windows.Devices.Custom)

CustomDevice </br> CustomDeviceContract </br> DeviceAccessMode </br> DeviceSharingMode </br> IOControlCode </br> KnownDeviceTypes

### [Windows.Devices.Enumeration](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration)

DeviceAccessChangedEventArgs </br> DeviceAccessInformation </br> DeviceAccessStatus </br> DeviceClass </br> DeviceConnectionChangeTriggerDetails </br> DeviceDisconnectButtonClickedEventArgs </br> DeviceInformation </br> DeviceInformationCollection </br> DeviceInformationCustomPairing </br> DeviceInformationKind </br> DeviceInformationPairing </br> DeviceInformationUpdate </br> DevicePairingKinds </br> DevicePairingProtectionLevel </br> DevicePairingRequestedEventArgs </br> DevicePairingResult </br> DevicePairingResultStatus </br> DevicePickerDisplayStatusOptions </br> DevicePickerFilter </br> DeviceSelectedEventArgs </br> DeviceThumbnail </br> DeviceUnpairingResult </br> DeviceUnpairingResultStatus </br> DeviceWatcher </br> DeviceWatcherEvent </br> DeviceWatcherEventKind </br> DeviceWatcherStatus </br> DeviceWatcherTriggerDetails </br> EnclosureLocation </br> IDevicePairingSettings </br> Panel

### [Windows.Devices.Enumeration.Pnp](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.Pnp)

PnpObject </br> PnpObjectCollection </br> PnpObjectType </br> PnpObjectUpdate </br> PnpObjectWatcher

### [Windows.Devices.Gpio](https://docs.microsoft.com/uwp/api/Windows.Devices.Gpio)

GpioChangeCount </br> GpioChangeCounter </br> GpioChangePolarity </br> GpioChangeReader </br> GpioChangeRecord </br> GpioController </br> GpioOpenStatus </br> GpioPin </br> GpioPinDriveMode </br> GpioPinEdge </br> GpioPinValue </br> GpioPinValueChangedEventArgs </br> GpioSharingMode

### [Windows.Devices.Gpio.Provider](https://docs.microsoft.com/uwp/api/Windows.Devices.Gpio.Provider)

GpioPinProviderValueChangedEventArgs </br> IGpioControllerProvider </br> IGpioPinProvider </br> IGpioProvider </br> ProviderGpioPinDriveMode </br> ProviderGpioPinEdge </br> ProviderGpioPinValue </br> ProviderGpioSharingMode

### [Windows.Devices.HumanInterfaceDevice](https://docs.microsoft.com/uwp/api/Windows.Devices.HumanInterfaceDevice)

HidBooleanControl </br> HidBooleanControlDescription </br> HidCollection </br> HidCollectionType </br> HidDevice </br> HidFeatureReport </br> HidInputReport </br> HidInputReportReceivedEventArgs </br> HidNumericControl </br> HidNumericControlDescription </br> HidOutputReport </br> HidReportType

### [Windows.Devices.I2c](https://docs.microsoft.com/uwp/api/Windows.Devices.I2c)

I2cBusSpeed </br> I2cConnectionSettings </br> I2cController </br> I2cDevice </br> I2cSharingMode </br> I2cTransferResult </br> I2cTransferStatus </br> II2cDeviceStatics

### [Windows.Devices.I2c.Provider](https://docs.microsoft.com/uwp/api/Windows.Devices.I2c.Provider)

II2cControllerProvider </br> II2cDeviceProvider </br> II2cProvider </br> ProviderI2cBusSpeed </br> ProviderI2cConnectionSettings </br> ProviderI2cSharingMode </br> ProviderI2cTransferResult </br> ProviderI2cTransferStatus

### [Windows.Devices.Power](https://docs.microsoft.com/uwp/api/Windows.Devices.Power)

Battery </br> BatteryReport

### [Windows.Devices.Pwm](https://docs.microsoft.com/uwp/api/Windows.Devices.Pwm)

PwmController </br> PwmPin </br> PwmPulsePolarity

### [Windows.Devices.Pwm.Provider](https://docs.microsoft.com/uwp/api/Windows.Devices.Pwm.Provider)

IPwmControllerProvider </br> IPwmProvider

### [Windows.Devices.Radios](https://docs.microsoft.com/uwp/api/Windows.Devices.Radios)

Radio </br> RadioAccessStatus </br> RadioKind </br> RadioState

### [Windows.Devices.Sensors](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors)

Accelerometer </br> AccelerometerDataThreshold </br> AccelerometerReading </br> AccelerometerReadingChangedEventArgs </br> AccelerometerReadingType </br> AccelerometerShakenEventArgs </br> ActivitySensor </br> ActivitySensorReading </br> ActivitySensorReadingChangeReport </br> ActivitySensorReadingChangedEventArgs </br> ActivitySensorReadingConfidence </br> ActivitySensorTriggerDetails </br> ActivityType </br> Altimeter </br> AltimeterReading </br> AltimeterReadingChangedEventArgs </br> Barometer </br> BarometerDataThreshold </br> BarometerReading </br> BarometerReadingChangedEventArgs </br> Compass </br> CompassDataThreshold </br> CompassReading </br> CompassReadingChangedEventArgs </br> Gyrometer </br> GyrometerDataThreshold </br> GyrometerReading </br> GyrometerReadingChangedEventArgs </br> HingeAngleReading </br> HingeAngleSensor </br> HingeAngleSensorReadingChangedEventArgs </br> ISensorDataThreshold </br>  Inclinometer </br> InclinometerDataThreshold </br> InclinometerReading </br> InclinometerReadingChangedEventArgs </br> LightSensor </br> LightSensorDataThreshold </br> LightSensorReading </br> LightSensorReadingChangedEventArgs </br> Magnetometer </br> MagnetometerAccuracy </br> MagnetometerDataThreshold </br> MagnetometerReading </br> MagnetometerReadingChangedEventArgs </br> OrientationSensor </br> OrientationSensorReading </br> OrientationSensorReadingChangedEventArgs </br> Pedometer </br> PedometerDataThreshold </br> PedometerReading </br> PedometerReadingChangedEventArgs </br> PedometerStepKind </br> ProximitySensor </br> ProximitySensorDataThreshold </br> ProximitySensorDisplayOnOffController </br> ProximitySensorReading </br> ProximitySensorReadingChangedEventArgs </br> SensorDataThresholdTriggerDetails </br> SensorOptimizationGoal </br> SensorQuaternion </br> SensorReadingType </br> SensorRotationMatrix </br> SensorType </br> SimpleOrientation </br> SimpleOrientationSensor </br> SimpleOrientationSensorOrientationChangedEventArgs

### [Windows.Devices.Sensors.Custom](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors.Custom)

CustomSensor </br> CustomSensorReading </br> CustomSensorReadingChangedEventArgs

### [Windows.Devices.SerialCommunication](https://docs.microsoft.com/uwp/api/Windows.Devices.SerialCommunication)

ErrorReceivedEventArgs </br> PinChangedEventArgs </br> SerialDevice </br> SerialError </br> SerialHandshake </br> SerialParity </br> SerialPinChange </br> SerialStopBitCount

### [Windows.Devices.Spi](https://docs.microsoft.com/uwp/api/Windows.Devices.Spi)

ISpiDeviceStatics </br> SpiBusInfo </br> SpiConnectionSettings </br> SpiController </br> SpiDevice </br> SpiMode </br> SpiSharingMode

### [Windows.Devices.Spi.Provider](https://docs.microsoft.com/uwp/api/Windows.Devices.Spi.Provider)

ISpiControllerProvider </br> ISpiDeviceProvider </br> ISpiProvider </br> ProviderSpiConnectionSettings </br> ProviderSpiMode </br> ProviderSpiSharingMode

### [Windows.Devices.Usb](https://docs.microsoft.com/uwp/api/Windows.Devices.Usb)

UsbBulkInEndpointDescriptor </br> UsbBulkInPipe </br> UsbBulkOutEndpointDescriptor </br> UsbBulkOutPipe </br> UsbConfiguration </br> UsbConfigurationDescriptor </br> UsbControlRecipient </br> UsbControlRequestType </br> UsbControlTransferType </br> UsbDescriptor </br> UsbDevice </br> UsbDeviceClass </br> UsbDeviceClasses </br> UsbDeviceDescriptor </br> UsbEndpointDescriptor </br> UsbEndpointType </br> UsbInterface </br> UsbInterfaceDescriptor </br> UsbInterfaceSetting </br> UsbInterruptInEndpointDescriptor </br> UsbInterruptInEventArgs </br> UsbInterruptInPipe </br> UsbInterruptOutEndpointDescriptor </br> UsbInterruptOutPipe </br> UsbReadOptions </br> UsbSetupPacket </br> UsbTransferDirection </br> UsbWriteOptions

### [Windows.Devices.WiFi](https://docs.microsoft.com/uwp/api/Windows.Devices.WiFi)

WiFiAccessStatus </br> WiFiAdapter </br> WiFiAvailableNetwork </br> WiFiConnectionMethod </br> WiFiConnectionResult </br> WiFiConnectionStatus </br> WiFiNetworkKind </br> WiFiNetworkReport </br> WiFiPhyKind </br> WiFiReconnectionKind </br> WiFiWpsConfigurationResult </br> WiFiWpsConfigurationStatus </br> WiFiWpsKind

## Windows.Foundation

### [Windows.Foundation](https://docs.microsoft.com/uwp/api/Windows.Foundation)

AsyncActionCompletedHandler </br> AsyncActionProgressHandler </br> AsyncActionWithProgressCompletedHandler </br> AsyncOperationCompletedHandler </br> AsyncOperationProgressHandler </br> AsyncOperationWithProgressCompletedHandler </br> AsyncStatus </br> DateTime </br> Deferral </br> DeferralCompletedHandler </br> EventHandler </br> EventRegistrationToken </br> FoundationContract </br> GuidHelper </br> HResult </br> IAsyncAction </br> IAsyncActionWithProgress </br> IAsyncInfo </br> IAsyncOperationWithProgress </br> IAsyncOperation </br> IClosable </br> IGetActivationFactory </br> IMemoryBuffer </br> IMemoryBufferReference </br> IPropertyValue </br> IReferenceArray </br> IReference </br> IStringable </br> IWwwFormUrlDecoderEntry </br> MemoryBuffer </br> Point </br> PropertyType </br> PropertyValue </br> Rect </br> Size </br> TimeSpan </br> TypedEventHandler </br> UniversalApiContract </br> Uri </br> WwwFormUrlDecoder </br> WwwFormUrlDecoderEntry

### [Windows.Foundation.Collections](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections)

CollectionChange </br> IIterable </br> IIterator </br> IKeyValuePair </br> IMapChangedEventArgs </br> IMapView </br> IMap </br> IObservableMap </br> IObservableVector </br> IPropertySet </br> IVectorChangedEventArgs </br> IVectorView </br> IVector </br> MapChangedEventHandler </br> PropertySet </br> StringMap </br> ValueSet </br> VectorChangedEventHandler

### [Windows.Foundation.Diagnostics](https://docs.microsoft.com/uwp/api/Windows.Foundation.Diagnostics)

AsyncCausalityTracer </br> CausalityRelation </br> CausalitySource </br> CausalitySynchronousWork </br> CausalityTraceLevel </br> ErrorDetails </br> ErrorOptions </br> FileLoggingSession </br> IErrorReportingSettings </br> IFileLoggingSession </br> ILoggingChannel </br> ILoggingSession </br> ILoggingTarget </br> LogFileGeneratedEventArgs </br> LoggingActivity </br> LoggingChannel </br> LoggingChannelOptions </br> LoggingFieldFormat </br> LoggingFields </br> LoggingLevel </br> LoggingOpcode </br> LoggingOptions </br> LoggingSession </br> RuntimeBrokerErrorSettings </br> TracingStatusChangedEventArgs

### [Windows.Foundation.Metadata](https://docs.microsoft.com/uwp/api/Windows.Foundation.Metadata)

ActivatableAttribute </br> AllowForWebAttribute </br> AllowMultipleAttribute </br> ApiContractAttribute </br> ApiInformation </br> AttributeNameAttribute </br> AttributeTargets </br> AttributeUsageAttribute </br> ComposableAttribute </br> CompositionType </br> ContractVersionAttribute </br> CreateFromStringAttribute </br> DefaultAttribute </br> DefaultOverloadAttribute </br> DeprecatedAttribute </br> DeprecationType </br> DualApiPartitionAttribute </br> ExclusiveToAttribute </br> ExperimentalAttribute </br> FastAbiAttribute </br> FeatureAttribute </br> FeatureStage </br> GCPressureAmount </br> GCPressureAttribute </br> GuidAttribute </br> HasVariantAttribute </br> InternalAttribute </br> LengthIsAttribute </br> MarshalingBehaviorAttribute </br> MarshalingType </br> MetadataMarshalAttribute </br> MuseAttribute </br> NoExceptionAttribute </br> OverloadAttribute </br> OverridableAttribute </br> Platform </br> PlatformAttribute </br> PreviousContractVersionAttribute </br> ProtectedAttribute </br> RangeAttribute </br> RemoteAsyncAttribute </br> StaticAttribute </br> ThreadingAttribute </br> ThreadingModel </br> VariantAttribute </br> VersionAttribute </br> WebHostHiddenAttribute

### [Windows.Foundation.Numerics](https://docs.microsoft.com/uwp/api/Windows.Foundation.Numerics)

Matrix3x2 </br> Matrix4x4 </br> Plane </br> Quaternion </br> Rational </br> Vector2 </br> Vector3 </br> Vector4

## Windows.Globalization

### [Windows.Globalization](https://docs.microsoft.com/uwp/api/Windows.Globalization)

ApplicationLanguages </br> Calendar </br> CalendarIdentifiers </br> ClockIdentifiers </br> CurrencyAmount </br> CurrencyIdentifiers </br> DayOfWeek </br> GeographicRegion </br> Language </br> LanguageLayoutDirection </br> NumeralSystemIdentifiers

### [Windows.Globalization.Collation](https://docs.microsoft.com/uwp/api/Windows.Globalization.Collation)

CharacterGrouping </br> CharacterGroupings

### [Windows.Globalization.DateTimeFormatting](https://docs.microsoft.com/uwp/api/Windows.Globalization.DateTimeFormatting)

DateTimeFormatter </br> DayFormat </br> DayOfWeekFormat </br> HourFormat </br> MinuteFormat </br> MonthFormat </br> SecondFormat </br> YearFormat

### [Windows.Globalization.NumberFormatting](https://docs.microsoft.com/uwp/api/Windows.Globalization.NumberFormatting)

CurrencyFormatter </br> CurrencyFormatterMode </br> DecimalFormatter </br> INumberFormatter </br> INumberFormatter2 </br> INumberFormatterOptions </br> INumberParser </br> INumberRounder </br> INumberRounderOption </br> ISignedZeroOption </br> ISignificantDigitsOption </br> IncrementNumberRounder </br> NumeralSystemTranslator </br> PercentFormatter </br> PermilleFormatter </br> RoundingAlgorithm </br> SignificantDigitsNumberRounder

### [Windows.Globalization.PhoneNumberFormatting](https://docs.microsoft.com/uwp/api/Windows.Globalization.PhoneNumberFormatting)

PhoneNumberFormat </br> PhoneNumberFormatter </br> PhoneNumberInfo </br> PhoneNumberMatchResult </br> PhoneNumberParseResult </br> PredictedPhoneNumberKind

## Windows.Graphics

### [Windows.Graphics](https://docs.microsoft.com/uwp/api/Windows.Graphics)

DisplayAdapterId

### [Windows.Graphics.DirectX](https://docs.microsoft.com/uwp/api/Windows.Graphics.DirectX)

DirectXPixelFormat

### [Windows.Graphics.DirectX.Direct3D11](https://docs.microsoft.com/uwp/api/Windows.Graphics.DirectX.Direct3D11)

Direct3DMultisampleDescription </br> Direct3DSurfaceDescription </br> IDirect3DDevice </br> IDirect3DSurface

### [Windows.Graphics.Display](https://docs.microsoft.com/uwp/api/Windows.Graphics.Display)

Windows.Graphics.Display.DisplayOrientations

### [Windows.Graphics.Imaging](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging)

> [!NOTE]
> Some APIs in this namespace have restrictions when used in the Windows ML container. See [**Limitations**](#limitations) for details.

BitmapAlphaMode </br> BitmapBounds </br> BitmapBuffer </br> BitmapBufferAccessMode </br> BitmapCodecInformation </br> BitmapDecoder </br> BitmapEncoder </br> BitmapFlip </br> BitmapFrame </br> BitmapInterpolationMode </br> BitmapPixelFormat </br> BitmapPlaneDescription </br> BitmapProperties </br> BitmapPropertiesView </br> BitmapPropertySet </br> BitmapRotation </br> BitmapSize </br> BitmapTransform </br> BitmapTypedValue </br> ColorManagementMode </br> ExifOrientationMode </br> IBitmapFrame </br> IBitmapFrameWithSoftwareBitmap </br> IBitmapPropertiesView </br> ImageStream </br> JpegSubsamplingMode </br> PixelDataProvider </br> PngFilterMode </br> SoftwareBitmap </br> TiffCompressionMode

## Windows.Media

### [Windows.Media](https://docs.microsoft.com/uwp/api/Windows.Media)

AudioBuffer </br> AudioBufferAccessMode </br> AudioFrame </br> AudioProcessing </br> AutoRepeatModeChangeRequestedEventArgs </br> IMediaExtension </br> IMediaFrame </br> IMediaMarker </br> IMediaMarkers </br> ImageDisplayProperties </br> MediaMarkerTypes </br> MediaPlaybackAutoRepeatMode </br> MediaPlaybackStatus </br> MediaPlaybackType </br> MediaProcessingTriggerDetails </br> MediaTimeRange </br> MediaTimelineController </br> MediaTimelineControllerFailedEventArgs </br> MediaTimelineControllerState </br> MusicDisplayProperties </br> PlaybackPositionChangeRequestedEventArgs </br> PlaybackRateChangeRequestedEventArgs </br> ShuffleEnabledChangeRequestedEventArgs </br> SoundLevel </br> SystemMediaTransportControls </br> SystemMediaTransportControlsButton </br> SystemMediaTransportControlsButtonPressedEventArgs </br> SystemMediaTransportControlsDisplayUpdater </br> SystemMediaTransportControlsProperty </br> SystemMediaTransportControlsPropertyChangedEventArgs </br> SystemMediaTransportControlsTimelineProperties </br> VideoDisplayProperties </br> VideoEffects </br> VideoFrame

## Windows.Networking

### [Windows.Networking](https://docs.microsoft.com/uwp/api/Windows.Networking)

DomainNameType </br> EndpointPair </br> HostName </br> HostNameSortOptions </br> HostNameType

### [Windows.Networking.BackgroundTransfer](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer)

BackgroundDownloadProgress </br> BackgroundTransferBehavior </br> BackgroundTransferCompletionGroup </br> BackgroundTransferCompletionGroupTriggerDetails </br> BackgroundTransferContentPart </br> BackgroundTransferCostPolicy </br> BackgroundTransferError </br> BackgroundTransferFileRange </br> BackgroundTransferGroup </br> BackgroundTransferPriority </br> BackgroundTransferRangesDownloadedEventArgs </br> BackgroundTransferStatus </br> BackgroundUploadProgress </br> ContentPrefetcher </br> DownloadOperation </br> IBackgroundTransferBase </br> IBackgroundTransferContentPartFactory </br> IBackgroundTransferOperation </br> IBackgroundTransferOperationPriority </br> ResponseInformation </br> UnconstrainedTransferRequestResult </br> UploadOperation

### [Windows.Networking.Connectivity](https://docs.microsoft.com/uwp/api/Windows.Networking.Connectivity)

AttributedNetworkUsage </br> CellularApnAuthenticationType </br> CellularApnContext </br> ConnectionCost </br> ConnectionProfile </br> ConnectionProfileDeleteStatus </br> ConnectionProfileFilter </br> ConnectionSession </br> ConnectivityInterval </br> ConnectivityManager </br> DataPlanStatus </br> DataPlanUsage </br> DataUsage </br> DataUsageGranularity </br> DomainConnectivityLevel </br> LanIdentifier </br> LanIdentifierData </br> NetworkAdapter </br> NetworkAuthenticationType </br> NetworkConnectivityLevel </br> NetworkCostType </br> NetworkEncryptionType </br> NetworkInformation </br> NetworkItem </br> NetworkSecuritySettings </br> NetworkStateChangeEventDetails </br> NetworkStatusChangedEventHandler </br> NetworkTypes </br> NetworkUsage </br> NetworkUsageStates </br> ProviderNetworkUsage </br> ProxyConfiguration </br> RoamingStates </br> RoutePolicy </br> TriStates </br> WlanConnectionProfileDetails </br> WwanConnectionProfileDetails </br> WwanContract </br> WwanDataClass </br> WwanNetworkIPKind </br> WwanNetworkRegistrationState

### [Windows.Networking.Sockets](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets)

BandwidthStatistics </br> ControlChannelTrigger </br> ControlChannelTriggerContract </br> ControlChannelTriggerResetReason </br> ControlChannelTriggerResourceType </br> ControlChannelTriggerStatus </br> DatagramSocket </br> DatagramSocketControl </br> DatagramSocketInformation </br> DatagramSocketMessageReceivedEventArgs </br> IControlChannelTriggerEventDetails </br> IControlChannelTriggerResetEventDetails </br> IWebSocket </br> IWebSocketControl </br> IWebSocketControl2 </br> IWebSocketInformation </br> IWebSocketInformation2 </br> MessageWebSocket </br> MessageWebSocketControl </br> MessageWebSocketInformation </br> MessageWebSocketMessageReceivedEventArgs </br> MessageWebSocketReceiveMode </br> RoundTripTimeStatistics </br> ServerMessageWebSocket </br> ServerMessageWebSocketControl </br> ServerMessageWebSocketInformation </br> ServerStreamWebSocket </br> ServerStreamWebSocketInformation </br> SocketActivityConnectedStandbyAction </br> SocketActivityContext </br> SocketActivityInformation </br> SocketActivityKind </br> SocketActivityTriggerDetails </br> SocketActivityTriggerReason </br> SocketError </br> SocketErrorStatus </br> SocketMessageType </br> SocketProtectionLevel </br> SocketQualityOfService </br> SocketSslErrorSeverity </br> StreamSocket </br> StreamSocketControl </br> StreamSocketInformation </br> StreamSocketListener </br> StreamSocketListenerConnectionReceivedEventArgs </br> StreamSocketListenerControl </br> StreamSocketListenerInformation </br> StreamWebSocket </br> StreamWebSocketControl </br> StreamWebSocketInformation </br> WebSocketClosedEventArgs </br> WebSocketError </br> WebSocketServerCustomValidationRequestedEventArgs

## Windows.Security

### [Windows.Security.Credentials](https://docs.microsoft.com/uwp/api/Windows.Security.Credentials)

PasswordCredential

### [Windows.Security.Cryptography](https://docs.microsoft.com/uwp/api/Windows.Security.Cryptography)

BinaryStringEncoding </br> CryptographicBuffer

### [Windows.Security.Cryptography.Certificates](https://docs.microsoft.com/uwp/api/Windows.Security.Cryptography.Certificates)

Certificate </br> CertificateChain </br> CertificateChainPolicy </br> CertificateEnrollmentManager </br> CertificateExtension </br> CertificateKeyUsages </br> CertificateQuery </br> CertificateRequestProperties </br> CertificateStore </br> CertificateStores </br> ChainBuildingParameters </br> ChainValidationParameters </br> ChainValidationResult </br> CmsAttachedSignature </br> CmsDetachedSignature </br> CmsSignerInfo </br> CmsTimestampInfo </br> EnrollKeyUsages </br> ExportOption </br> InstallOptions </br> KeyAlgorithmNames </br> KeyAttestationHelper </br> KeyProtectionLevel </br> KeySize </br> KeyStorageProviderNames </br> PfxImportParameters </br> SignatureValidationResult </br> StandardCertificateStoreNames </br> SubjectAlternativeNameInfo </br> UserCertificateEnrollmentManager </br> UserCertificateStore

### [Windows.Security.Cryptography.Core](https://docs.microsoft.com/uwp/api/Windows.Security.Cryptography.Core)

AsymmetricAlgorithmNames </br> AsymmetricKeyAlgorithmProvider </br> Capi1KdfTargetAlgorithm </br> CryptographicEngine </br> CryptographicHash </br> CryptographicKey </br> CryptographicPadding </br> CryptographicPrivateKeyBlobType </br> CryptographicPublicKeyBlobType </br> EccCurveNames </br> EncryptedAndAuthenticatedData </br> HashAlgorithmNames </br> HashAlgorithmProvider </br> KeyDerivationAlgorithmNames </br> KeyDerivationAlgorithmProvider </br> KeyDerivationParameters </br> MacAlgorithmNames </br> MacAlgorithmProvider </br> PersistedKeyProvider </br> SymmetricAlgorithmNames </br> SymmetricKeyAlgorithmProvider

### [Windows.Security.Cryptography.DataProtection](https://docs.microsoft.com/uwp/api/Windows.Security.Cryptography.DataProtection)

DataProtectionProvider

### [Windows.Security.EnterpriseData](https://docs.microsoft.com/uwp/api/Windows.Security.EnterpriseData)

BufferProtectUnprotectResult </br> DataProtectionInfo </br> DataProtectionManager </br> DataProtectionStatus </br> EnforcementLevel </br> EnterpriseDataContract </br> FileProtectionInfo </br> FileProtectionManager </br> FileProtectionStatus </br> FileRevocationManager </br> FileUnprotectOptions </br> ProtectedAccessResumedEventArgs </br> ProtectedAccessSuspendingEventArgs </br> ProtectedContainerExportResult </br> ProtectedContainerImportResult </br> ProtectedContentRevokedEventArgs </br> ProtectedFileCreateResult </br> ProtectedImportExportStatus </br> ProtectionPolicyAuditAction </br> ProtectionPolicyAuditInfo </br> ProtectionPolicyEvaluationResult </br> ProtectionPolicyManager </br> ProtectionPolicyRequestAccessBehavior </br> ThreadNetworkContext

### [Windows.Security.ExchangeActiveSyncProvisioning](https://docs.microsoft.com/uwp/api/Windows.Security.ExchangeActiveSyncProvisioning)

EasClientDeviceInformation </br> EasClientSecurityPolicy </br> EasComplianceResults </br> EasContract </br> EasDisallowConvenienceLogonResult </br> EasEncryptionProviderType </br> EasMaxInactivityTimeLockResult </br> EasMaxPasswordFailedAttemptsResult </br> EasMinPasswordComplexCharactersResult </br> EasMinPasswordLengthResult </br> EasPasswordExpirationResult </br> EasPasswordHistoryResult </br> EasRequireEncryptionResult

## Windows.Storage

### [Windows.Storage](https://docs.microsoft.com/uwp/api/Windows.Storage)

AppDataPaths </br> ApplicationData </br> ApplicationDataCompositeValue </br> ApplicationDataContainer </br> ApplicationDataContainerSettings </br> ApplicationDataCreateDisposition </br> ApplicationDataLocality </br> ApplicationDataSetVersionHandler </br> CachedFileManager </br> CreationCollisionOption </br> DownloadsFolder </br> FileAccessMode </br> FileAttributes </br> FileIO </br> IStorageFile </br> IStorageFile2 </br> IStorageFilePropertiesWithAvailability </br> IStorageFolder </br> IStorageFolder2 </br> IStorageItem </br> IStorageItem2 </br> IStorageItemProperties </br> IStorageItemProperties2 </br> IStorageItemPropertiesWithProvider </br> IStreamedFileDataRequest </br> KnownFolderId </br> KnownFolders </br> KnownFoldersAccessStatus </br> KnownLibraryId </br> NameCollisionOption </br> PathIO </br> SetVersionDeferral </br> SetVersionRequest </br> StorageDeleteOption </br> StorageFile </br> StorageFolder </br> StorageItemTypes </br> StorageLibrary </br> StorageLibraryChange </br> StorageLibraryChangeReader </br> StorageLibraryChangeTracker </br> StorageLibraryChangeType </br> StorageOpenOptions </br> StorageProvider </br> StorageStreamTransaction </br> StreamedFileDataRequest </br> StreamedFileDataRequestedHandler </br> StreamedFileFailureMode </br> SystemAudioProperties </br> SystemDataPaths </br> SystemGPSProperties </br> SystemImageProperties </br> SystemMediaProperties </br> SystemMusicProperties </br> SystemPhotoProperties </br> SystemProperties </br> SystemVideoProperties </br> UserDataPaths

### [Windows.Storage.AccessCache](https://docs.microsoft.com/uwp/api/Windows.Storage.AccessCache)

AccessCacheOptions </br> AccessListEntry </br> AccessListEntryView </br> IStorageItemAccessList </br> ItemRemovedEventArgs </br> RecentStorageItemVisibility </br> StorageApplicationPermissions </br> StorageItemAccessList </br> StorageItemMostRecentlyUsedList

### [Windows.Storage.BulkAccess](https://docs.microsoft.com/uwp/api/Windows.Storage.BulkAccess)

FileInformation </br> FileInformationFactory </br> FolderInformation </br> IStorageItemInformation

### [Windows.Storage.Compression](https://docs.microsoft.com/uwp/api/Windows.Storage.Compression)

CompressAlgorithm </br> Compressor </br> Decompressor

### [Windows.Storage.FileProperties](https://docs.microsoft.com/uwp/api/Windows.Storage.FileProperties)

BasicProperties </br> DocumentProperties </br> IStorageItemExtraProperties </br> ImageProperties </br> MusicProperties </br> PhotoOrientation </br> PropertyPrefetchOptions </br> StorageItemContentProperties </br> StorageItemThumbnail </br> ThumbnailMode </br> ThumbnailOptions </br> ThumbnailType </br> VideoOrientation </br> VideoProperties

### [Windows.Storage.Provider](https://docs.microsoft.com/uwp/api/Windows.Storage.Provider)

CachedFileOptions </br> CachedFileTarget </br> CachedFileUpdater </br> CachedFileUpdaterUI </br> CloudFilesContract </br> FileUpdateRequest </br> FileUpdateRequestDeferral </br> FileUpdateRequestedEventArgs </br> FileUpdateStatus </br>  IStorageProviderItemPropertySource </br> IStorageProviderPropertyCapabilities </br> IStorageProviderUriSource </br> ReadActivationMode </br> StorageProviderFileTypeInfo </br> StorageProviderGetContentInfoForPathResult </br> StorageProviderGetPathForContentUriResult </br> StorageProviderHardlinkPolicy </br> StorageProviderHydrationPolicy </br> StorageProviderHydrationPolicyModifier </br> StorageProviderInSyncPolicy </br> StorageProviderItemProperties </br> StorageProviderItemProperty </br> StorageProviderItemPropertyDefinition </br> StorageProviderPopulationPolicy </br> StorageProviderProtectionMode </br> StorageProviderSyncRootInfo </br> StorageProviderSyncRootManager </br> StorageProviderUriSourceStatus </br> UIStatus </br> WriteActivationMode

### [Windows.Storage.Search](https://docs.microsoft.com/uwp/api/Windows.Storage.Search)

CommonFileQuery </br> CommonFolderQuery </br> ContentIndexer </br> ContentIndexerQuery </br> DateStackOption </br> FolderDepth </br> IIndexableContent </br> IStorageFolderQueryOperations </br> IStorageQueryResultBase </br> IndexableContent </br> IndexedState </br> IndexerOption </br> QueryOptions </br> SortEntry </br> SortEntryVector </br> StorageFileQueryResult </br> StorageFolderQueryResult </br> StorageItemQueryResult </br> StorageLibraryChangeTrackerTriggerDetails </br> StorageLibraryContentChangedTriggerDetails </br> ValueAndLanguage

### [Windows.Storage.Streams](https://docs.microsoft.com/uwp/api/Windows.Storage.Streams)

Buffer </br> ByteOrder </br> DataReader </br> DataReaderLoadOperation </br> DataWriter </br> DataWriterStoreOperation </br> FileInputStream </br> FileOpenDisposition </br> FileOutputStream </br> FileRandomAccessStream </br> IBuffer </br>  IContentTypeProvider </br> IDataReader </br> IInputStream </br> IInputStreamReference </br> IOutputStream </br> IRandomAccessStream </br> IRandomAccessStreamReference </br> IRandomAccessStreamWithContentType </br> InMemoryRandomAccessStream </br> InputStreamOptions </br> InputStreamOverStream </br> OutputStreamOverStream </br> RandomAccessStream </br> RandomAccessStreamOverStream </br> RandomAccessStreamReference </br> UnicodeEncoding

## Windows.System

### [Windows.System](https://docs.microsoft.com/uwp/api/Windows.System)

AppDiagnosticInfoWatcherStatus </br> AppExecutionStateChangeResult </br> AppMemoryReport </br> AppMemoryUsageLevel </br> AppMemoryUsageLimitChangingEventArgs </br> AppResourceGroupBackgroundTaskReport </br> AppResourceGroupEnergyQuotaState </br> AppResourceGroupExecutionState </br> AppResourceGroupInfoWatcherStatus </br> AppResourceGroupMemoryReport </br> AppResourceGroupStateReport </br> AppUriHandlerHost </br> AppUriHandlerRegistration </br> AppUriHandlerRegistrationManager </br> AutoUpdateTimeZoneStatus </br> DateTimeSettings </br> DiagnosticAccessStatus </br> DispatcherQueue </br> DispatcherQueueController </br> DispatcherQueueHandler </br> DispatcherQueuePriority </br> DispatcherQueueShutdownStartingEventArgs </br> DispatcherQueueTimer </br> KnownUserProperties </br> LaunchFileStatus </br> LaunchQuerySupportStatus </br> LaunchQuerySupportType </br> LaunchUriResult </br> LaunchUriStatus </br> MemoryManager </br> PowerState </br> ProcessLauncher </br> ProcessLauncherOptions </br> ProcessLauncherResult </br> ProcessMemoryReport </br> ProcessorArchitecture </br> ProtocolForResultsOperation </br> RemoteLaunchUriStatus </br> RemoteLauncherOptions </br> ShutdownKind </br> ShutdownManager </br> SystemManagementContract </br> TimeZoneSettings

### [Windows.System.Diagnostics](https://docs.microsoft.com/uwp/api/Windows.System.Diagnostics)

DiagnosticActionResult </br> DiagnosticActionState </br> DiagnosticInvoker </br> ProcessCpuUsage </br> ProcessCpuUsageReport </br> ProcessDiskUsage </br> ProcessDiskUsageReport </br> ProcessMemoryUsage </br> ProcessMemoryUsageReport </br> SystemCpuUsage </br> SystemCpuUsageReport </br> SystemDiagnosticInfo </br> SystemMemoryUsage </br> SystemMemoryUsageReport

### [Windows.System.Diagnostics.Telemetry](https://docs.microsoft.com/uwp/api/Windows.System.Diagnostics.Telemetry)

PlatformTelemetryClient </br> PlatformTelemetryRegistrationResult </br> PlatformTelemetryRegistrationSettings </br> PlatformTelemetryRegistrationStatus

### [Windows.System.Diagnostics.TraceReporting](https://docs.microsoft.com/uwp/api/Windows.System.Diagnostics.TraceReporting)

PlatformDiagnosticActionState </br> PlatformDiagnosticActions </br> PlatformDiagnosticEscalationType </br> PlatformDiagnosticEventBufferLatencies </br> PlatformDiagnosticTraceInfo </br> PlatformDiagnosticTracePriority </br> PlatformDiagnosticTraceRuntimeInfo </br> PlatformDiagnosticTraceSlotState </br> PlatformDiagnosticTraceSlotType

### [Windows.System.Display](https://docs.microsoft.com/uwp/api/Windows.System.Display)

DisplayRequest

### [Windows.System.Inventory](https://docs.microsoft.com/uwp/api/Windows.System.Inventory)

InstalledDesktopApp

### [Windows.System.Power](https://docs.microsoft.com/uwp/api/Windows.System.Power)

BackgroundEnergyManager </br> BatteryStatus </br> EnergySaverStatus </br> ForegroundEnergyManager </br> PowerManager </br> PowerSupplyStatus

### [Windows.System.Power.Diagnostics](https://docs.microsoft.com/uwp/api/Windows.System.Power.Diagnostics)

BackgroundEnergyDiagnostics </br> ForegroundEnergyDiagnostics

### [Windows.System.Profile](https://docs.microsoft.com/uwp/api/Windows.System.Profile)

AnalyticsInfo </br> AnalyticsVersionInfo </br> AppApplicability </br> EducationSettings </br> HardwareIdentification </br> HardwareToken </br> KnownRetailInfoProperties </br> PlatformDataCollectionLevel </br> PlatformDiagnosticsAndUsageDataSettings </br> ProfileHardwareTokenContract </br> ProfileRetailInfoContract </br> ProfileSharedModeContract </br> RetailInfo </br> SharedModeSettings </br> SystemIdentification </br> SystemIdentificationInfo </br> SystemIdentificationSource </br> SystemOutOfBoxExperienceState </br> SystemSetupInfo </br> UnsupportedAppRequirement </br> UnsupportedAppRequirementReasons </br> WindowsIntegrityPolicy

### [Windows.System.Profile.SystemManufacturers](https://docs.microsoft.com/uwp/api/Windows.System.Profile.SystemManufacturers)

OemSupportInfo </br> SmbiosInformation </br> SystemManufacturersContract </br> SystemSupportDeviceInfo </br> SystemSupportInfo

### [Windows.System.Threading](https://docs.microsoft.com/uwp/api/Windows.System.Threading)

ThreadPool </br> ThreadPoolTimer </br> TimerDestroyedHandler </br> TimerElapsedHandler </br> WorkItemHandler </br> WorkItemOptions </br> WorkItemPriority

### [Windows.System.Threading.Core](https://docs.microsoft.com/uwp/api/Windows.System.Threading.Core)

PreallocatedWorkItem </br> SignalHandler </br> SignalNotifier

### [Windows.System.User](https://docs.microsoft.com/uwp/api/Windows.System.User)

User </br> UserAuthenticationStatus </br> UserAuthenticationStatusChangeDeferral </br> UserAuthenticationStatusChangingEventArgs </br> UserChangedEventArgs </br> UserDeviceAssociation </br> UserDeviceAssociationChangedEventArgs </br> UserPicker </br> UserPictureSize </br> UserType </br> UserWatcher </br> UserWatcherStatus </br> UserWatcherUpdateKind </br> VirtualKey </br> VirtualKeyModifiers

## Windows.UI

### [Windows.UI.Text.Core](https://docs.microsoft.com/uwp/api/Windows.UI.Text.Core)

CoreTextInputScope

## Windows.Web

### [Windows.Web](https://docs.microsoft.com/uwp/api/Windows.Web)

IUriToStreamResolver </br> WebError </br> WebErrorStatus

### [Windows.Web.AtomPub](https://docs.microsoft.com/uwp/api/Windows.Web.AtomPub)

AtomPubClient </br> ResourceCollection </br> ServiceDocument </br> Workspace

### [Windows.Web.Http](https://docs.microsoft.com/uwp/api/Windows.Web.Http)

HttpBufferContent </br> HttpClient </br> HttpCompletionOption </br> HttpCookie </br> HttpCookieCollection </br> HttpCookieManager </br> HttpFormUrlEncodedContent </br> HttpGetBufferResult </br> HttpGetInputStreamResult </br> HttpGetStringResult </br> HttpMethod </br> HttpMultipartContent </br> HttpMultipartFormDataContent </br> HttpProgress </br> HttpProgressStage </br> HttpRequestMessage </br> HttpRequestResult </br> HttpResponseMessage </br> HttpResponseMessageSource </br> HttpStatusCode </br> HttpStreamContent </br> HttpStringContent </br> HttpTransportInformation </br> HttpVersion </br> IHttpContent

### [Windows.Web.Http.Diagnostics](https://docs.microsoft.com/uwp/api/Windows.Web.Http.Diagnostics)

HttpDiagnosticProvider </br> HttpDiagnosticProviderRequestResponseCompletedEventArgs </br> HttpDiagnosticProviderRequestResponseTimestamps </br> HttpDiagnosticProviderRequestSentEventArgs </br> HttpDiagnosticProviderResponseReceivedEventArgs </br> HttpDiagnosticRequestInitiator </br> HttpDiagnosticSourceLocation </br> HttpDiagnosticsContract

### [Windows.Web.Http.Filters](https://docs.microsoft.com/uwp/api/Windows.Web.Http.Filters)

HttpBaseProtocolFilter </br> HttpCacheControl </br> HttpCacheReadBehavior </br> HttpCacheWriteBehavior </br> HttpCookieUsageBehavior </br> HttpServerCustomValidationRequestedEventArgs </br> IHttpFilter

### [Windows.Web.Http.Headers](https://docs.microsoft.com/uwp/api/Windows.Web.Http.Headers)

HttpCacheDirectiveHeaderValueCollection </br> HttpChallengeHeaderValue </br> HttpChallengeHeaderValueCollection </br> HttpConnectionOptionHeaderValue </br> HttpConnectionOptionHeaderValueCollection </br> HttpContentCodingHeaderValue </br> HttpContentCodingHeaderValueCollection </br> HttpContentCodingWithQualityHeaderValue </br> HttpContentCodingWithQualityHeaderValueCollection </br> HttpContentDispositionHeaderValue </br> HttpContentHeaderCollection </br> HttpContentRangeHeaderValue </br> HttpCookiePairHeaderValue </br> HttpCookiePairHeaderValueCollection </br> HttpCredentialsHeaderValue </br> HttpDateOrDeltaHeaderValue </br> HttpExpectationHeaderValue </br> HttpExpectationHeaderValueCollection </br> HttpLanguageHeaderValueCollection </br> HttpLanguageRangeWithQualityHeaderValue </br> HttpLanguageRangeWithQualityHeaderValueCollection </br> HttpMediaTypeHeaderValue </br> HttpMediaTypeWithQualityHeaderValue </br> HttpMediaTypeWithQualityHeaderValueCollection </br> HttpMethodHeaderValueCollection </br> HttpNameValueHeaderValue </br> HttpProductHeaderValue </br> HttpProductInfoHeaderValue </br> HttpProductInfoHeaderValueCollection </br> HttpRequestHeaderCollection </br> HttpResponseHeaderCollection </br> HttpTransferCodingHeaderValue </br> HttpTransferCodingHeaderValueCollection

### [Windows.Web.Syndication](https://docs.microsoft.com/uwp/api/Windows.Web.Syndication)

ISyndicationClient </br> ISyndicationNode </br> ISyndicationText </br> RetrievalProgress </br> SyndicationAttribute </br> SyndicationCategory </br> SyndicationClient </br> SyndicationContent </br> SyndicationError </br> SyndicationErrorStatus </br> SyndicationFeed </br> SyndicationFormat </br> SyndicationGenerator </br> SyndicationItem </br> SyndicationLink </br> SyndicationNode </br> SyndicationPerson </br> SyndicationText </br> SyndicationTextType </br> TransferProgress

## Limitations

### Windows.Graphics.Imaging

Several APIs in the [Windows.Graphics.Imaging](https://docs.microsoft.com/uwp/api/windows.graphics.imaging) namespace are subject to limitations when used in the Windows ML container. These restrictions are as follows.

* Windows.Graphics.Imaging supports encoding and decoding for JPG, PNG, GIF, TIFF, and BMP file formats only.
* If using the BMP format with BI_BITFIELDS compression, only the formats 16bppBGR555, 16bppBGR565, and 32bppBGRA are supported.
* There is no support for CMYK, High Dynamic Range, Half-float pixel formats, Fixed-point pixel formats, or images with more than four channels.
* There is no support for 32bppRGBE, 16bppYQuantizedDctCoefficients, 16bppCbQuantizedDctCoefficients, and 16bppCrQuantizedDctCoefficients pixel formats.
* There is no support for reading or writing XMP metadata or [Photo Metadata Policies](https://docs.microsoft.com/windows/win32/wic/photo-metadata-policies). In transcoding, the API will attempt to preserve XMP metadata if possible.
* There is no support for colorspace manipulation and conversation.
* There is *partial* support for the `ColorManageToSRgb` flag in [GetPixelDataAsync](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapdecoder.getpixeldataasync) and [GetSoftwareBitmapAsync](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapframe.getsoftwarebitmapasync). If the flag is specified, the operation will succeed if the image has a commonly-recognized sRGB color profile. Otherwise, the HRESULT equivalent of `ERROR_ICM_NOT_ENABLED` will be returned.
