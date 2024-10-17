{
        match self {
            UserInfoError::ActixWebErr(error) => {
                println!("\n UserInfoError ActixWebErr : {}", error);
                HttpResponse::NotFound()
                    .content_type("application/json; charset=utf-8")
                    .json(CustomErrorResponse {
                        code: fetch_code(CustomErrorStr::ActixWebError), // "Err0001".to_string(),
                        message: "Actix web Error".to_string(),
                    })
            }
            UserInfoError::FileNotFound => {
                println!("\n UserInfoError FileNotFound");
                HttpResponse::InternalServerError()
                    .content_type("application/json; charset=utf-8")
                    .json(CustomErrorResponse {
                        code: fetch_code(CustomErrorStr::FileNotFound),
                        message: "File Not Found".to_string(),
                    })
            }
            UserInfoError::SerdeErr(error) => {
                println!("\n UserInfoError SerdeErr {}", error);
                HttpResponse::InternalServerError()
                    .content_type("application/json; charset=utf-8")
                    .json(CustomErrorResponse {
                        code: fetch_code(CustomErrorStr::SerdeError),
                        message: "Serde Error".to_string(),
                    })
            }
            UserInfoError::IoErr(error) => {
                println!("\n UserInfoError IoErr {}", error);
                HttpResponse::InternalServerError()
                    .content_type("application/json; charset=utf-8")
                    .json(CustomErrorResponse {
                        code: fetch_code(CustomErrorStr::IoError),
                        message: "File IO Error".to_string(),
                    })
            }
        }
    }


use serde::{Deserialize, Serialize};

#[derive(Debug, Serialize, Deserialize)]
pub struct CustomErrorResponse {
    pub code: CustomErrorCode,
    pub message: String,
}

#[derive(Debug, Serialize, Deserialize)]
pub enum CustomErrorCode {
    ERR0001,
    ERR0002,
    ERR0003,
    ERR0004,
}

#[derive(Debug, Serialize, Deserialize)]
pub enum CustomErrorStr {
    ActixWebError,
    SerdeError,
    IoError,
    FileNotFound,
}

pub fn fetch_code(custom_err_str: CustomErrorStr) -> CustomErrorCode {
    match custom_err_str {
        CustomErrorStr::ActixWebError => CustomErrorCode::ERR0001,
        CustomErrorStr::SerdeError => CustomErrorCode::ERR0002,
        CustomErrorStr::IoError => CustomErrorCode::ERR0003,
        CustomErrorStr::FileNotFound => CustomErrorCode::ERR0004,
    }
}

fn fetch_error(custom_err_str: CustomErrorStr, message: String) -> CustomErrorResponse {
    match custom_err_str {
        CustomErrorStr::ActixWebError => CustomErrorResponse {
            code: fetch_code(CustomErrorStr::ActixWebError), // "Err0001".to_string(),
            message: message,
        },
        CustomErrorStr::SerdeError => CustomErrorResponse {
            code: fetch_code(CustomErrorStr::FileNotFound),
            message: message,
        },
        CustomErrorStr::IoError => CustomErrorResponse {
            code: fetch_code(CustomErrorStr::SerdeError),
            message: message,
        },
        CustomErrorStr::FileNotFound => CustomErrorResponse {
            code: fetch_code(CustomErrorStr::IoError),
            message: message,
        },
    }
}

// impl CustomErrorResponse {
//     fn fetch_error(code: CustomErrorCode) -> Self {
//         match code {
//             CustomErrorCode::ERR0001 => Self {
//                 code :CustomErrorCode::ERR0001,
//                 message: "Actix web Error".to_string()
//             },
//             CustomErrorCode::ERR0002 => Self {
//                 code: CustomErrorCode::ERR0001,
//                 message: "".to_string(),
//             },
//             CustomErrorCode::ERR0003 => Self {
//                 code :CustomErrorCode::ERR0001,
//                 message: "".to_string(),
//             },
//             CustomErrorCode::ERR0004 => Self {
//                 code :CustomErrorCode::ERR0001,
//                 message: "".to_string(),
//             }
//         }
//     }
// }


    
