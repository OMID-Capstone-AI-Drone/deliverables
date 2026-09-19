# Constraints Essay
OMID AI-Based Farm Disease, Pest, Yield, and Frost Detection System 

Team Members: Andrea Dall'Ara, Bridget Trout, Dang Luu, Cyrus Speicher 

## Economic
Economic constraints directly affect this project because OMID USA is sponsoring the combined drone, sensor, AI, and web-app system with a total budget of $2,000. To stay within that limit, the team will reuse the current drone and existing multispectral camera where practical and favor open or freely available software over proprietary agricultural systems that cannot be modified through an API. This keeps the design aligned with OMID USA's stated goal of developing affordable and sustainable technology for smallholder farmers.

## Security
Security factors into the project because the web app will combine vineyard sensor readings, drone imagery, AI predictions, and farmer-configurable settings in one system. The project requirements state that only authorized members should be able to access the app, so authentication and access control must be part of the core architecture. The app will also retain configuration and model versions and provide logging, error handling, and data-quality indicators so results can be traced when a prediction is reviewed.  

## Environmental  
Environmental constraints matter because the system will be tested in an actual Cincinnati vineyard and can also use a greenhouse for year-round testing. The stationary sensor boxes therefore need weatherproofing, reliable wireless communication, and efficient battery or solar power, while the drone needs repeatable acquisition settings for changing field conditions. These constraints make hardware and software that can last long outdoor deployments more viable than solutions designed only for controlled indoor testing.  

## Social  
Social constraints also apply because OMID USA's mission is to create affordable solutions that help smallholder farmers become more self-sufficient, and the project is intended to be adaptable to new regions, crops, and changing climates. A concrete trade-off is that satisfying the economic constraint of staying within the $2,000 budget puts pressure on the security and reliability constraints, since stronger access controls, ruggedized hardware, calibration, and reliable data collection can increase costs. To balance these constraints, the team will prioritize existing hardware and open-source software while reserving the budget for essential security, calibration, and field-reliability needs before optional interface features or nonessential hardware upgrades. 