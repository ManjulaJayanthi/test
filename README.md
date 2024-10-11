
use serde::{Deserialize, Serialize};

#[derive(Serialize, Deserialize, Debug, Clone,PartialEq)]
pub struct UserinfoFileResponse {
    pub id: String,
    pub name: String,
}


use std::{
    env,
    fs::{self, File},
    path::Path,
};

use crate::user::{UserInfoError, UserinfoFileResponse};

use super::user_response::GetUserinfoFileResult;

pub fn fetch_userinfo_from_file(id: String) -> GetUserinfoFileResult {
    let mut n_path = Path::new(".").join("src");
    n_path.push(env::var("MOCK_DATA_FILE").expect("MOCK_DATA_FILE missing"));
    dbg!(&n_path);
    match File::open(n_path.clone()) {
        Err(_why) => Err(UserInfoError::FileNotFound),
        Ok(file) => {
            println!(">>> {:?}", &file);
            let data_str = fs::read_to_string(n_path)?; //.expect("Unable to read file MOCK_DATA_FILE");

            let data_vec: Vec<UserinfoFileResponse> = serde_json::from_str(&data_str)?;
            //.expect("Unable to parse the MOCK_DATA_FILE content");

            println!("{:?}", data_vec);

            let data_option = data_vec.iter().find(|c| c.id == id).cloned();
            match data_option {
                Some(data) => Ok(data),
                None => Err(UserInfoError::FileNotFound),
            }
        }
    }
}
